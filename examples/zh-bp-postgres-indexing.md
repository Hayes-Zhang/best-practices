# 示例：`/bp` 深度调研 — PostgreSQL 索引设计最佳实践

> **⚠️ 说明 1（版本）**：本示例创建于 v2.1 时代，采用三级置信度
> (Found / Synthesized / Deduced) 章节结构。**v2.2 起新增了 🧩 Adapted 这一级**
> （邻近场景借鉴 + 三段式定制），完整 4 级示范见
> [`en-auto-mode-adapted-example.md`](en-auto-mode-adapted-example.md)。本示例的
> 报告骨架仍然有效，只是缺少独立的"定制建议（Adapted）"章节。
>
> **⚠️ 说明 2（数据）**：这是一个**示例性输出**（illustrative example），用来展示
> `/bp` 命令在 3-agent 并行模式下的**报告格式与质量基线**，不是真实运行结果。
>
> 文中提及的来源域名（PostgreSQL 官方文档、Percona 工程博客等）是真实存在的官方
> 来源，但具体页面 URL、置信度评分、来源数量等数字均为示意，运行真实 `/bp` 时
> 会根据当时的搜索结果重新生成。**请勿把本示例的具体引用当作事实依据。**
>
> **触发命令**：`/bp "PostgreSQL 索引设计最佳实践 高写入场景"`

---

# 最佳实践指南：PostgreSQL 索引设计（高写入场景）

> **研究深度**：深度（3 视角，12 个独立来源）
> **置信度**：高（领域成熟，原理稳定）
> **生成日期**：2026-05-19

## 执行摘要

PostgreSQL 索引设计在高写入场景下的核心矛盾是：**查询加速 vs 写入开销 + 膨胀风险**。
2026 年的共识是用 **B-tree + 部分索引** 处理热点字段，**BRIN** 处理时序数据，
**HOT update** + 合理 `fillfactor` 缓解 WAL 写入压力。盲目加索引比不加索引更危险。

---

## 一、已有成熟实践

### 实践 1：部分索引（Partial Index）减少索引体积
- **做法**：对高基数字段加 `WHERE` 条件的部分索引，仅索引活跃记录
- **来源**：[PostgreSQL 官方文档 — Partial Indexes](https://www.postgresql.org/docs/current/indexes-partial.html)、[Percona 工程博客](https://www.percona.com/blog/)
- **适用场景**：状态字段（如 `status = 'active'`）、软删除（`deleted_at IS NULL`）
- **交叉验证**：scout + analyst 一致确认 ✅
- **trade-off**：查询条件必须匹配索引的 `WHERE`，否则索引不会被使用

### 实践 2：BRIN 索引处理时序/append-only 数据
- **做法**：对按时间顺序写入的大表用 BRIN 索引（块级范围索引）
- **来源**：[PostgreSQL 官方文档 — BRIN](https://www.postgresql.org/docs/current/brin.html)、[Citus 博客](https://www.citusdata.com/blog/)
- **适用场景**：日志表、时间序列数据，单表 > 1 亿行
- **交叉验证**：scout 找到，analyst 确认 ⭐⭐⭐
- **trade-off**：仅在数据物理顺序 ≈ 索引列顺序时有效；适合写入有序但不适合随机写入

### 实践 3：合理设置 `fillfactor` 启用 HOT update
- **做法**：对频繁 UPDATE 的表设 `fillfactor=70~85`，留出页内空间避免索引膨胀
- **来源**：[PostgreSQL Wiki — HOT](https://wiki.postgresql.org/wiki/HOT)、[2ndQuadrant 博客](https://www.2ndquadrant.com/)
- **适用场景**：UPDATE 频繁、被更新字段非索引字段的表
- **交叉验证**：creator + analyst 一致确认 ✅
- **trade-off**：表占用空间增加 ~15-30%

---

## 二、综合建议

### 建议 1：先 EXPLAIN 再加索引
- **依据**：综合多个生产事故复盘（Percona、Notion、Stripe 工程博客），> 60% 的"性能索引"
  实际从未被查询使用，反而拖慢写入
- **置信度说明**：来源都是大型生产环境，但每个公司的 workload pattern 不同，建议你自己
  跑一次 `pg_stat_user_indexes` 验证当前索引使用率

### 建议 2：单表索引数控制在 5 个以内
- **依据**：3 个独立来源（Percona、Crunchy Data、PostgreSQL 邮件列表）的一致建议
- **置信度说明**：5 是经验值，不是硬性上限。原则是"每个索引必须能说出被哪个具体 query 用"

---

## 三、原创推荐

### 推荐 1：索引检视清单（推导）
**推导逻辑**：综合上述实践，提取出可执行的 checklist：

```
□ 这个索引被哪个具体 SQL 查询使用？（不能说出来 → 删）
□ 这个查询每天执行频率？（< 100 次/天且非业务关键 → 重新评估）
□ pg_stat_user_indexes 显示 idx_scan = 0 持续 30 天？（删）
□ 这个表的写入频率 > 读取频率？（考虑部分索引或 BRIN）
□ 索引大小 / 表大小 > 30%？（膨胀警告，考虑 REINDEX）
```

**验证建议**：在 staging 环境跑 1 周清单后的 schema，对比 QPS 和 WAL 写入量。

---

## 方案对比表

| 索引类型 | 写入开销 | 查询加速 | 存储开销 | 适合场景 |
|---------|---------|---------|---------|---------|
| B-tree (默认) | 中 | 强（等值/范围） | 中 | 主键、高基数字段 |
| Partial Index | 低（仅匹配行）| 强（条件查询） | 低 | 状态字段、软删除 |
| BRIN | 极低 | 中（范围扫描） | 极低 | 时序数据、append-only |
| GIN | 高 | 强（JSONB/全文）| 高 | JSONB、全文搜索 |
| Hash | 中 | 强（仅等值） | 中 | 等值查询（很少用，B-tree 通常更好）|

---

## 来源与权威评估

| # | 来源 | 类型 | 权威等级 | 时效性 | 关键贡献 |
|---|------|------|---------|--------|---------|
| 1 | PostgreSQL 官方文档 | 官方文档 | ⭐⭐⭐ | 持续更新 | 索引类型与使用约束 |
| 2 | Percona 工程博客 | 专家博客 | ⭐⭐⭐ | 2025-Q4 | 生产环境踩坑案例 |
| 3 | Citus / Crunchy Data 博客 | 厂商博客 | ⭐⭐ | 2026-Q1 | BRIN 与分区表实践 |
| ... | （略，共 12 个） | | | | |

---

## 置信度与局限性

### 高置信
- B-tree、Partial Index、BRIN 的基本原理和使用约束
- HOT update + fillfactor 调优

### 中置信
- 具体的 fillfactor 数值（70 vs 80 vs 85）依赖具体表的更新模式
- 索引数量"5 个"上限是经验值，不同 workload 不同

### 低置信 / 待验证
- 新的 PostgreSQL 17 特性（如某些 query 的 parallel index scan 优化）在你具体版本是否生效

### 信息盲区
- 云托管 PostgreSQL（如 RDS、Cloud SQL）的索引调优可能受厂商配置限制，本调研未涵盖

---

## 这个示例展示的核心能力

1. **多视角合成**：scout 找资料、analyst 评质量、creator 推导清单
2. **诚实分级**：✅ 成熟 / 🔧 综合 / 💡 原创 三档明确标注
3. **决策导向**：不是罗列信息，而是给出可执行的索引检视清单
4. **盲区透明**：明确说哪些没覆盖（云托管 PG、新版本特性）
5. **来源溯源**：每条结论都能追到具体出处

# 权威来源清单

按领域分类的高质量来源。搜索时优先查这些来源，找到的信息可信度更高。

---

## 软件工程 & 架构

### 一手来源（官方/标准）
- **官方文档 Best Practices 页**：Next.js、React、Vue、Django、Rails 等框架的官方推荐
- **RFC / 标准文档**：IETF RFC、W3C 规范、TC39 提案
- **云厂商架构指南**：AWS Well-Architected Framework、Google Cloud Architecture Center、Azure Architecture Center
- **Google SRE Book**：站点可靠性工程的权威参考

### 二手来源（专家/社区共识）
- **GitHub 高星项目**：看实际实现方式，尤其是 README 中的 best practices 章节
- **Stack Overflow 高票答案**：社区投票验证过的方案
- **engineering blogs**：Netflix Tech Blog、Uber Engineering、Stripe Blog、Airbnb Engineering
- **技术大会**：Google I/O、WWDC、React Conf、KubeCon 演讲

---

## AI / LLM / Agent

### 一手来源
- **Anthropic 文档**：Claude API docs、prompt engineering guide、Claude Code docs
- **OpenAI 文档**：API reference、cookbook、best practices guide
- **HuggingFace 文档**：模型使用指南、transformers best practices

### 二手来源
- **AI 工程师博客**：Simon Willison、Lilian Weng、Eugene Yan
- **社区实践**：Claude Code Discord、r/ClaudeAI、r/LocalLLaMA
- **GitHub awesome lists**：awesome-claude-code、awesome-llm-apps
- **Twitter/X AI 圈**：关键意见领袖的实践分享

---

## 产品设计 & UX

### 一手来源
- **Nielsen Norman Group (NNGroup)**：可用性研究的学术标准
- **Apple Human Interface Guidelines**：iOS/macOS 设计规范
- **Google Material Design**：Android/Web 设计系统
- **W3C WCAG**：无障碍设计标准

### 二手来源
- **Lenny's Newsletter**：产品管理实践
- **Reforge**：增长和产品策略
- **SVPG (Marty Cagan)**：产品团队最佳实践
- **设计系统**：Ant Design、Radix、shadcn/ui 的设计决策文档

---

## 中文技术生态

### 高质量来源
- **知乎专栏**：技术领域的长文分析（注意筛选高赞/专家作者）
- **InfoQ CN**：企业级技术实践
- **掘金 / SegmentFault**：开发者社区（以投票数和收藏数判断质量）
- **公众号技术大号**：字节/阿里/腾讯技术团队的官方号
- **V2EX**：开发者讨论（注意区分意见和事实）

### 需要谨慎的来源
- CSDN（质量参差不齐，优先看其他来源）
- 百度搜索结果（SEO 污染严重，优先用 Google 搜中文内容）

---

## Claude Code / AI 编程工具

### 一手来源
- **Anthropic 官方文档**：Claude Code docs、CLAUDE.md 规范
- **Claude Code GitHub**：官方仓库的 issues、discussions、README
- **Anthropic Blog**：官方发布的使用指南和案例

### 社区来源
- **awesome-claude-code** (GitHub)：社区整理的 skills/plugins/tips
- **Claude Code Discord**：用户交流和问题解答
- **Twitter/X #ClaudeCode**：实时分享的使用技巧
- **YouTube/Bilibili**：Claude Code 教程和实操视频

---

## 通用搜索策略

搜索时，用以下关键词组合提高命中率：

```
英文：
  "{topic} best practices"
  "{topic} recommended approach"
  "{topic} how to properly"
  "{topic} production ready"
  "{topic} lessons learned"
  "awesome {topic}" site:github.com

中文：
  "{主题} 最佳实践"
  "{主题} 推荐做法"
  "{主题} 踩坑经验"
  "{主题} 生产环境"
  "{主题} 实战经验"
```

---

## 邻近场景识别策略（用于 🧩 Adapted tier）

当直接搜索（上面策略）返回的结果不足以支撑 Found / Synthesized 时，**不要直接退到 Deduced**——先识别"邻近场景"，作为 Adapted 模式的原料。

### 维度 1：约束相似性

找"核心约束相同"的邻近场景。约束维度举例：

| 当前场景 | 核心约束 | 邻近场景（约束相似） |
|---|---|---|
| AI 笔记应用本地优先同步 | 离线优先 + 冲突解决 + 操作流 | Notion / Obsidian / Linear sync engine |
| 实时聊天内容审核 | 高吞吐 + 低延迟 + 噪声信号分类 | 邮件反垃圾、广告竞价审核 |
| API 设计 | 演化约束 + 向后兼容 + 接口契约 | 数据库表设计、消息队列 schema 设计 |
| 新员工 1on1 | 权力不对称对话 + 信息提取 | 客户访谈、code review 对话 |
| Vibe coder 选 LLM 接入方案 | 多服务整合 + 成本 + 切换灵活性 | SaaS 选型（如 Stripe vs PayPal）、CDN 选型 |
| 小团队周会模板 | 同步信息成本 + 决策推进 | 敏捷 standup、客户成功 QBR |

### 维度 2：社群成熟度（验证邻近方案能否借鉴）

不是任何邻近场景都值得借鉴。优先选满足以下条件的邻近场景：

- ✅ **≥3 年实践积累**——避免还在迭代中的方案
- ✅ **≥2 个独立权威来源**——避免某一家公司的特殊做法
- ✅ **可量化评估指标**——有客观成功/失败指标
- ⚠️ **领域不能太远**——核心约束必须真的相似，不是名义上像

### 搜索关键词模板

```
英文：
  "{adjacent topic} best practices"
  "{adjacent topic} lessons learned"
  "{adjacent topic} how to"
  "borrowed from {field-X}" "{topic}"
  "adapted from {field-X}" "{topic}"
  "{topic} inspired by"
  "{constraint type} design patterns"

中文：
  "{邻近主题} 最佳实践"
  "{邻近主题} 经验"
  "{邻近主题} 怎么做"
  "{领域} 借鉴 {另一领域}"
```

### Adapted 的红线（避免伪邻近迁移）

- ❌ **名义引用而非真实借鉴**：声称"借鉴 X"，但方案细节与 X 的真实做法没有对应
- ❌ **借鉴过于通用**：原则太抽象（如"模块化"、"解耦"），没有具体可执行的迁移
- ❌ **回避 domain shift 风险**：没有明确指出"这个邻近场景的某条约束在当前场景不成立"

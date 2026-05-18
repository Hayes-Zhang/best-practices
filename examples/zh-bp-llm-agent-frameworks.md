# 示例：`/bp` 深度调研 — LLM Agent 编排框架对比

> **⚠️ 说明**：这是一个**示例性输出**（illustrative example），用来展示 `/bp` 命令
> 在 3-agent 并行模式下的**报告格式与质量基线**，不是真实运行结果。
>
> 文中提及的来源域名（LangChain blog、Anthropic Engineering 等）是真实存在的官方
> 来源，但具体页面 URL、置信度评分、"14 个独立来源"等数字均为示意，运行真实
> `/bp` 时会根据当时的搜索结果重新生成。**请勿把本示例的具体引用当作事实依据。**
>
> **触发命令**：`/bp "LLM Agent 编排框架对比 2026"`

---

# 最佳实践指南：LLM Agent 编排框架选型（2026）

> **研究深度**：深度（3 视角，14 个独立来源）
> **置信度**：中（领域变化快，6 个月窗口内信息）
> **生成日期**：2026-05-19

## 执行摘要

LLM Agent 编排领域在 2026 年呈现三足鼎立：**LangGraph** 主导生产级状态机场景，
**CrewAI** 在多角色协作上更轻量，**Anthropic Agent SDK** 凭借 Claude Code 生态
快速崛起。选型核心取决于三个问题：是否需要持久化状态、agent 数量、是否绑定 Claude 模型。

---

## 一、已有成熟实践

### 实践 1：单 agent + tool calling（基线方案）
- **做法**：直接用 OpenAI/Anthropic SDK 的原生 tool calling，不引入框架
- **来源**：[Anthropic 工程博客](https://www.anthropic.com/engineering)、[OpenAI Cookbook](https://github.com/openai/openai-cookbook)
- **适用场景**：1-3 个工具，单轮或简单多轮，无需跨会话状态
- **交叉验证**：scout + analyst 一致确认 ✅
- **trade-off**：复杂工作流要自己写状态机，成本随复杂度指数上升

### 实践 2：LangGraph 状态机模式
- **做法**：用 LangGraph 的 `StateGraph` 定义节点 + 边，状态作为一等公民
- **来源**：[LangChain Blog](https://blog.langchain.dev/)、[LangGraph 官方教程](https://langchain-ai.github.io/langgraph/)
- **适用场景**：5+ 节点、需要 checkpoint/resume、有人工介入步骤
- **交叉验证**：scout + creator 一致 ✅，analyst 标注"学习曲线陡"

### 实践 3：CrewAI 多角色协作
- **做法**：定义 `Agent` + `Task` + `Crew`，agent 之间通过任务委派协作
- **来源**：[CrewAI 文档](https://docs.crewai.com/)、[GitHub 30k+ stars](https://github.com/joaomdmoura/crewAI)
- **适用场景**：3-7 个分工明确的角色（research/write/review 类）
- **交叉验证**：scout 找到，analyst 评 ⭐⭐

---

## 二、综合建议

### 建议 1：长任务用 LangGraph，短任务用原生 SDK
- **依据**：综合 5 个生产案例（来源略），LangGraph 的 checkpoint 在 >10 分钟任务
  上节省大量重跑成本；但 <5 分钟任务的 boilerplate 开销得不偿失
- **置信度说明**：基于公开案例数有限，企业内部数据未公开

### 建议 2：Anthropic Agent SDK 适合 Claude-only 场景
- **依据**：Agent SDK 与 Claude Code/Claude Desktop 生态深度绑定，但锁定 Claude 模型
- **置信度说明**：发布较新（2026 Q1），生产案例尚不丰富

---

## 三、原创推荐

### 推荐 1：选型决策树（推导）
**推导逻辑**：

```
需要持久化状态/checkpoint？
├─ 是 → LangGraph
└─ 否 → agent 数量？
        ├─ 1-2 个 → 原生 SDK
        └─ 3+ 个 → 角色分工明确？
                  ├─ 是 → CrewAI
                  └─ 否 → LangGraph（更通用）
```

**验证建议**：用你最复杂的工作流跑 1 周 PoC，看实际遇到的痛点是否被框架覆盖。

---

## 方案对比表

| 方案 | 学习曲线 | 状态管理 | 生态成熟度 | 模型绑定 | 适合场景 |
|------|---------|---------|-----------|---------|---------|
| 原生 tool calling | ⭐ 低 | ❌ 无 | ⭐⭐⭐⭐⭐ | 中性 | 简单单 agent |
| LangGraph | ⭐⭐⭐⭐ 高 | ✅ 一流 | ⭐⭐⭐⭐ | 中性 | 复杂状态机 |
| CrewAI | ⭐⭐ 中 | 🟡 弱 | ⭐⭐⭐ | 中性 | 角色协作 |
| Anthropic SDK | ⭐⭐ 中 | ✅ 良好 | ⭐⭐⭐ | Claude only | Claude 生态 |

---

## 来源与权威评估

| # | 来源 | 类型 | 权威等级 | 时效性 | 关键贡献 |
|---|------|------|---------|--------|---------|
| 1 | LangChain 官方文档 | 官方文档 | ⭐⭐⭐ | 2026-Q2 | LangGraph API 细节 |
| 2 | Anthropic Engineering 博客 | 官方博客 | ⭐⭐⭐ | 2026-Q1 | Agent 设计模式 |
| 3 | CrewAI GitHub README | 官方仓库 | ⭐⭐ | 2026-Q2 | CrewAI 用例 |
| ... | （略，共 14 个） | | | | |

---

## 置信度与局限性

### 高置信
- LangGraph 适合复杂状态机
- 原生 SDK 适合简单场景

### 中置信
- CrewAI 在多角色场景的实际效果（生产案例较少）

### 低置信 / 待验证
- Anthropic Agent SDK 在大规模生产环境的稳定性（发布太新）

### 信息盲区
- 国内框架（如 Dify、Coze）的英文资料较少，本次调研覆盖不足

---

## 这个示例展示的核心能力

1. **多视角合成**：scout 找资料、analyst 评质量、creator 推导决策树
2. **诚实分级**：✅ 成熟 / 🔧 综合 / 💡 原创 三档明确标注
3. **决策导向**：不是罗列信息，而是给出选型决策树
4. **盲区透明**：明确说哪些没覆盖（如国内框架）
5. **来源溯源**：每条结论都能追到具体出处

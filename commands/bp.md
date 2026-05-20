---
description: Deep best-practices research on any topic (technical or otherwise) with a 3-agent team (scout/analyst/creator), cross-validation, and a saved Markdown report. Escape hatch when the auto-search skill's confidence isn't enough, or when you need a persistent artifact. Usage: /bp "<topic>"
---

# /bp — Deep Best-Practices Research

> **When to use `/bp`** (English summary)
>
> This is the **escape hatch**, not the default. The `best-practices` skill already
> performs lightweight auto-search during normal conversations.
>
> Use `/bp` only when:
> - Auto-search confidence was too low and you need 3-agent depth
> - You need a multi-option comparison with full trade-offs
> - You want a saved Markdown report for your team or future reference
> - The topic is complex enough that a single search angle misses too much
>
> **Topics can be anything** — technical (e.g. "Next.js authentication libraries"),
> product (e.g. "onboarding flow design"), process (e.g. "engineering oncall
> rotations"), or life skills (e.g. "negotiating salary at a startup"). The
> 3-agent investigation works the same way regardless of domain.
>
> Cost: ~5–10 min and ~3–5× the tokens of a normal turn (3 Opus agents in parallel).
> Output: Markdown report saved under `./best-practices/<topic-slug>-guide.md`.
>
> The rest of this file is the agent's operational playbook (Chinese, for the
> orchestrator model). End users don't need to read past this point.

---

# /bp — 严肃深度调研

> **何时该用 /bp？**
>
> 这个命令是兜底入口，不是常规入口。大部分场景下，对话中提到技术问题时 best-practices skill 会**自动**做轻量搜索 + 评估 + 内联回答。
>
> 在以下情况才需要显式用 /bp：
> - 自动搜索置信度不够，需要 3 agent 深度调研
> - 需要多方案对比（如 X vs Y vs Z 的完整 trade-off）
> - 需要保存为文件，作为团队/未来参考资料
> - 复杂主题，单视角搜索覆盖不够

> **执行模式：Agent Team（强制）**
> 本命令必须使用 Agent Team 模式执行，禁止退化为单 agent 模式。
> - ✅ 必须调用 TeamCreate 创建团队
> - ✅ 必须使用 Agent 的 team_name + name 参数生成 teammate
> - ✅ teammate 之间必须通过 SendMessage 协作
> - ❌ 禁止使用纯 Subagent（即不带 team_name 的 Agent 调用）
> - ❌ 禁止跳过 TeamCreate 直接启动 agent

**研究主题**：$ARGUMENTS

---

## 核心理念

**找到的 > 拼出来的 > 借鉴定制的 > 凭原理推的。**

四种产出质量，**必须诚实标注**：
- ✅ **成熟实践（Found）**：多个权威来源在**同场景**一致推荐，可放心采用
- 🔧 **综合实践（Synthesized）**：从多个**同场景**碎片来源拼出来的，方向可靠但细节需调整
- 🧩 **定制实践（Adapted）**：同场景无直接答案，但**邻近场景**有成熟方案——通过三段式产出（邻近方案 → 共性原则 → 当前场景定制）。每条原则都有邻近案例锚点
- 💡 **原创实践（Deduced）**：同场景**和邻近场景**都无可借鉴的成熟方案，基于核心约束第一性原理推导（最后兜底）

**关键约束**：
- 🧩 **Adapted ≠ 拼凑**：必须找到 1-2 个真实邻近场景的成熟方案、提炼共性原则、明确"当前场景调整"。"借鉴"不能只是名义引用——必须有具体方案对应
- 💡 **Deduced ≠ 编造**：必须展示完整推导链，用户能判断推理是否成立
- **优先 Adapted 而非 Deduced**：跳过 Adapted 直接 Deduced 是最常见的 AI 失败模式。**只有真的找不到邻近成熟方案时**，才退到 Deduced

**绝不能做**：把 AI 生成的建议包装成"业界最佳实践"。不确定就说不确定。

---

## 阶段 1：理解需求

分析用户提供的研究主题 `$ARGUMENTS`。

- 主题明确 → 直接进入阶段 2
- 主题模糊或过于宽泛 → 用 AskUserQuestion 追问：
  - 你具体想解决什么问题？
  - 有没有已经知道但不满意的方案？
  - 这个最佳实践要应用在什么场景下？

确定：
1. **核心问题**：用户到底想知道什么
2. **搜索语言**：中文优先 / 英文优先 / 两者都搜
3. **领域分类**（无限制——只要这件事已经有人想过/做过）：
   - 技术工程、AI/LLM、产品设计、运营增长、工具使用
   - 沟通管理（1:1、汇报、谈薪、招聘、远程协作等）
   - 生活技能（育儿、烹饪、健身、理财等）
   - 商业策略（融资、市场进入、定价等）
   - 其他任何有公认实践的领域

---

## 阶段 2：创建团队 & 分配任务

### 2.1 创建研究团队

```
TeamCreate:
  team_name: "bp-{timestamp}"
  description: "最佳实践深度调研：[主题摘要]"
```

`{timestamp}` 用当前时间戳（如 `20260519-143000`）确保唯一。

**验证**：TeamCreate 调用成功后，再继续。

### 2.2 创建 3 个研究任务

```
TaskCreate × 3：
  1. 搜索现有最佳实践 — 广撒网搜索所有现有实践、教程、指南
  2. 分析与评估 — 评估搜索结果质量，对比方案优劣
  3. 填补空白与创造 — 识别盲区，优先邻近场景适配（Adapted），无邻近时第一性原理推导（Deduced）
```

### 2.3 并行启动 3 个 Agent

**重要**：必须在**一条消息**中并行发起所有 3 个 Agent 调用，不要串行启动。

每个 Agent 调用**必须**包含以下参数（缺一不可）：
- `subagent_type`: 插件 agent 名称（`best-practices:scout` / `:analyst` / `:creator`）
- `team_name`: 与 2.1 创建的团队名称**完全一致**
- `name`: teammate 名称（`scout` / `analyst` / `creator`）
- `prompt`: 研究任务描述
- `model`: `opus`
- `mode`: `"bypassPermissions"`
- `description`: 简短描述

**禁止事项**：
- ❌ 省略 `team_name` 参数
- ❌ 省略 `name` 参数
- ❌ 串行启动

#### Prompt 模板

```
Agent tool:
  subagent_type: "best-practices:scout"  (或 analyst / creator)
  team_name: "bp-{timestamp}"
  name: "scout"  (或 analyst / creator)
  mode: "bypassPermissions"
  prompt: |
    ## 研究任务

    **研究主题**：[用户的研究主题]

    **你的角色**：搜索员（或 分析员 / 创造员）
    **你的名称**：scout（或 analyst / creator）
    **团队名称**：bp-{timestamp}

    请从你的专业角度对上述主题的最佳实践进行研究。

    **工作流程**：
    1. 用 TaskGet 读取被分配的任务详情
    2. 用 TaskUpdate 将任务标记为 in_progress
    3. 按照搜索策略/分析框架/创造方法开展工作
    4. 完成初步工作后，将 2-3 个最关键的发现通过 SendMessage 发送给 team lead
       - 先读取 ~/.claude/teams/{团队名称}/config.json 获取 team lead 的名称
       - 使用 SendMessage: type="message", recipient="{team-lead-name}", content=你的发现
    5. 收到 team lead 转发的其他视角发现时，从专业角度交叉评估
    6. 按输出格式模板生成完整报告
    7. 用 TaskUpdate 将任务标记为 completed
    8. 每个来源必须附 URL
    9. 诚实评估置信度

    **搜索语言**：[中文优先/英文优先/两者都搜]
    **搜索建议**：[根据主题给出具体的搜索关键词建议]

  description: "[角色名]视角研究"
  model: opus
```

#### 3 个 Agent 清单

| # | Agent | Teammate Name | 角色 | 颜色 |
|---|-------|---------------|------|------|
| 1 | best-practices:scout | scout | 🔍 搜索员 | 🔵 |
| 2 | best-practices:analyst | analyst | 🔬 分析员 | 🟢 |
| 3 | best-practices:creator | creator | 💡 创造员 | 🟡 |

### 2.4 分配任务

```
TaskUpdate × 3：
  { taskId: "1", owner: "scout" }
  { taskId: "2", owner: "analyst" }
  { taskId: "3", owner: "creator" }
```

---

## 阶段 3：协作研究（Hub-and-Spoke）

> **自检**：阶段 2 没调 TeamCreate？立即停止并从 2.1 重新开始。

作为 Team Lead，你的协作职责：

### 3.1 接收关键发现

各 agent 完成初步研究后，会通过 SendMessage 向你发送关键发现。

### 3.2 交叉验证转发

| Agent | 自然关联 | 转发逻辑 |
|-------|---------|---------|
| scout | analyst | 搜索结果 → 转发给 analyst 评估质量 |
| analyst | creator | 信息空白 → 转发给 creator 填补 |
| creator | scout | 需要补充搜索的方向 → 转发给 scout |

**转发示例**：

```
SendMessage:
  type: "message"
  recipient: "analyst"
  content: |
    来自 scout 的搜索结果需要你评估：
    1. [scout 的发现内容]
    请用质量评估框架打分，判断这些来源是否可信。
  summary: "转发 scout 发现给 analyst 评估"
```

### 3.3 等待研究完成

用 TaskList 检查所有任务状态。3 个任务都 completed → 进入阶段 4。

---

## 阶段 4：综合分析

基于 3 份报告和交叉验证结果：

1. **来源整合**：合并 scout 找到的所有来源（含 Tier 5 邻近场景源），去重
2. **质量排序**：按 analyst 的评估结果排序
3. **四级分层归类**：
   - ✅ **成熟实践（Found）**：scout 直接找到 + analyst 高分确认（同场景多源共识）
   - 🔧 **综合实践（Synthesized）**：analyst 从多个**同场景**碎片来源综合的建议
   - 🧩 **定制实践（Adapted）**：analyst 路由为 "Adaptation candidate" + creator 通过 Method 0 完成三段式产出（邻近方案 → 共性原则 → 当前场景定制）
   - 💡 **原创实践（Deduced）**：analyst 路由为 "Deduction candidate" + creator 通过 Method 1 第一性原理推导（无邻近可借鉴时）
4. **交叉验证**：标注哪些发现经过多视角确认
5. **路径完整性自检**：如果报告里出现 💡 Deduced，检查是否真的搜过邻近场景——优先 Adapted 是 /bp 的核心质量原则

---

## 阶段 5：交付报告

### 5.1 在对话中输出

按 `${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/output-templates.md` 的「模板 B：最佳实践指南」格式输出。

### 5.2 保存文件

用 Write 工具保存到当前工作目录下的 `./best-practices/` 子目录：

**默认路径**：`./best-practices/{topic-slug}-guide.md`

如果用户在全局对话中调用 `/bp`（即当前工作目录是 `$HOME`），fallback 到 `~/best-practices/{topic-slug}-guide.md`。

`{topic-slug}` 用英文短横线格式（如 `nextjs-authentication`、`llm-agent-orchestration`）。

**可配置**：用户可以在 plugin 加载时通过环境变量 `BEST_PRACTICES_OUTPUT_DIR` 覆盖默认路径。如果该变量已设置，优先使用它。

### 5.3 告知用户

```
📄 最佳实践指南已生成：
- 📝 文件：{完整路径}
```

用 `open` 命令打开所在文件夹（macOS）。

---

## 阶段 6：清理团队

### 6.1 关闭所有 agent

**一条消息**中并行发送 shutdown_request：

```
SendMessage × 3：
  { type: "shutdown_request", recipient: "scout", content: "研究完成，感谢。" }
  { type: "shutdown_request", recipient: "analyst", content: "研究完成，感谢。" }
  { type: "shutdown_request", recipient: "creator", content: "研究完成，感谢。" }
```

### 6.2 等待确认 & 删除团队

所有 agent 确认关闭后，用 `TeamDelete` 清理资源。

---

## 注意事项

- 综合报告不偏向任何单一视角——搜索/分析/创造三者并重
- 明确标注信息的可信度和来源
- 对不确定信息用"可能"、"据报道"等限定词
- 所有非直接答案必须明确标 🧩 **Adapted（标注邻近场景来源）** 或 💡 **Deduced（展示推导链）**，**不能冒充 ✅ Found**
- 报告用中文，技术术语保留英文原文
- Hub-and-spoke 通信，不要广播
- Agent idle 是正常状态，不需要特别处理
- 整体时间目标：5-10 分钟完成，效果优先于速度
- 单次 /bp 大约消耗 3-5 倍普通对话的 token 量，注意成本提示

---

## 与 SKILL.md 自动模式的关系

| 场景 | 使用什么 |
|------|---------|
| 对话中提到技术问题 | SKILL.md 自动搜索 + 评估，30 秒内联回答 |
| 自动搜索后置信度不够 | 在回答末尾建议跑 `/bp` |
| 用户想保存调研报告 | 显式跑 `/bp` |
| 用户想多视角对比 | 显式跑 `/bp` |
| 用户主动想严肃调研 | 显式跑 `/bp` |

`/bp` 不替代日常对话——它是**用户认真想做一次研究**时的工具。

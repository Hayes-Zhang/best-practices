# Best Practices for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code/plugins)

一个 Claude Code 插件——当你问技术问题时，**它会默默查证最新做法**，
并且**拒绝把不靠谱的来源当真理喂给你**。

**[English README](README.md)**

---

## 为什么需要它

LLM 训练数据过时得很快。框架经常出 breaking change，API 改版，"推荐做法"
每 6 个月就变一次。你问 Claude *"Next.js 怎么做认证？"* 拿到的可能是
2024 年正确、现在已经过时两个版本的答案。

但简单粗暴的"每次都搜网"也有问题：SEO 文章、AI 灌水内容、社区互相矛盾
的帖子，会以更"权威"的样子把模型带偏。

这个插件夹在两个失败模式中间——**该搜的时候搜，搜回来的内容做结构化的
反向思考评估，并对自己的回答标置信度**，让你清楚什么时候可以信、什么时候
该自己再深挖。

## 它做什么

```
┌─────────────────────────────────────────────────────────────┐
│ 自动模式（默认）                                              │
│                                                             │
│ 检测到技术问题 → 静默搜索                                     │
│ → 跑反向思考质量评估                                          │
│ → 标置信度（✅ 高 / 🔧 中 / ⚠️ 低）                          │
│ → 把答案融入正常回复，附来源和 trade-off                       │
│                                                             │
│ 时间预算：约 30 秒。不需要任何命令。                           │
└─────────────────────────────────────────────────────────────┘
                          ↓
              （需要更深时升级）
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 深度模式 — /bp "主题"                                        │
│                                                             │
│ 3 个 agent（scout/analyst/creator）并行调研，                │
│ Hub-and-Spoke 协作，多来源交叉验证，                          │
│ 保存为 Markdown 报告。                                       │
│                                                             │
│ 时间预算：5–10 分钟。手动触发。                               │
└─────────────────────────────────────────────────────────────┘
```

## 快速开始

### 安装

```bash
# 添加 marketplace
/plugin marketplace add hayes-zhang/best-practices

# 安装插件
/plugin install best-practices@best-practices
```

### 试一下

在新对话里随便问一个框架/版本相关问题：

> *"Next.js 15 怎么做认证？"*

插件会静默搜索 + 评估 + 在回答末尾标注来源和置信度。不用记任何命令。

如果想要更深入的多方案对比：

```
/bp "Next.js 15 认证方案对比"
```

更多真实输出见 **[examples/](examples/)**。

## 触发清单

插件设计为自动激活，但只在真的有价值时触发。

| ✅ 触发 | ❌ 不触发 |
|---|---|
| 框架/库的用法（*"怎么用 X 做 Y"*） | 基础概念（*"什么是闭包"*） |
| 选型对比（*"X vs Y"*） | 数学/算法（20 年没变过） |
| 明示时效（*"最新的"*、*"2026 的"*） | 修具体代码 bug |
| 明示版本号（*"React 19"*、*"Tailwind v4"*） | 创意任务（写文章、起名字） |
| 变化快领域（AI、前端、云服务、加密货币） | 用户已经选定方案，只问执行细节 |
| 小众/新出工具 | 元对话 |
| 明确关键词（*"最佳实践"*、*"推荐"*） | |

## 工作原理

### 三层判断

1. **触发检测**——这个问题值得搜吗？
2. **反向思考评估**（默默进行，回答前完成）：
   - 这些来源真的独立吗？还是都引用同一个原始出处？
   - 我搜过反面证据吗？
   - 这些建议有 trade-off 吗？还是所有人都在吹？
   - 信息有多新？这个领域变化多快？
3. **置信度分级回答**：
   - **✅ 高**——多个权威来源一致 → 直接给建议
   - **🔧 中**——碎片化共识 → 给建议但标注"综合多方"
   - **⚠️ 低**——来源不可靠 → *拒绝给建议*，提示下一步路径

### 五个红旗

插件主动检测并明示：

- 🚩 **利益冲突**——来源在推销自己的产品
- 🚩 **SEO 文章特征**——标题党、内容空泛、没有具体示例
- 🚩 **AI 生成嫌疑**——高度通用、缺少具体经验
- 🚩 **银弹思维**——复杂问题只给一个方案，没有 trade-off
- 🚩 **独立性假象**——多个来源其实都引用同一个原始出处

## Trust & Safety

整个插件围绕一个原则：**诚实的来源标注 > 自信的瞎猜**。

- 每个建议必须有真实 URL。URL 不存在就不编造
- 低置信度回答**拒绝**给直接答案，明确解释为什么不能给，告诉你下一步
- AI 推导的建议会显式标"推导"，不会伪装成"业界共识"
- 插件对自己的局限保持诚实

如果发现它自信地说错了，[提个 issue](https://github.com/hayes-zhang/best-practices/issues)——
这是最有价值的贡献。

## 配置

| 选项 | 默认值 | 怎么改 |
|---|---|---|
| `/bp` 报告保存目录 | `./best-practices/`（当前工作目录） | 设环境变量 `BEST_PRACTICES_OUTPUT_DIR` |
| 自动搜索时间预算 | 30 秒 | 编辑 `skills/best-practices/SKILL.md` |
| 权威来源清单 | 见 [authority-sources.md](skills/best-practices/references/authority-sources.md) | 直接改文件（欢迎 PR） |

## 要求

### 自动模式（对话中默认行为）

- 支持 plugin 的 **Claude Code**
- 可用的 **WebSearch / WebFetch** 工具
- 可选：**Context7 MCP** 用于查询框架一手文档

### `/bp` 深度模式

- 以上全部，加上：
- 环境中可用的 **Agent Team API** 工具：`TeamCreate`、`TeamDelete`、
  `TaskCreate`、`TaskUpdate`、`TaskGet`、`TaskList`、`SendMessage`，
  以及支持 `team_name` + `name` 参数的 `Agent` 工具。
  这些是启动 3 agent（scout/analyst/creator）并行调研团队的前提
- 推荐用 **Opus 模型**跑 agents（3 个并发实例——这是 `/bp` 最主要的成本来源）
- 充足的 token 预算：一次 `/bp` 大约消耗**普通对话 3-5 倍的 token**

> **注意**：如果你的环境不支持 Agent Team API，`/bp` 将无法启动团队。
> 自动模式可以正常使用。兼容性请见 [issues](https://github.com/hayes-zhang/best-practices/issues)。

## 局限性

- 插件的判断质量取决于模型本身。置信度分级是启发式的，不是统计意义上的概率
- `/bp` 消耗约普通对话 3–5 倍的 token（3 个 Opus agent 并行）。谨慎使用
- 自动搜索会让被触发的回答多 10–30 秒延迟。第一句"让我查一下最新的方案……"
  就是为了让你知道它在干活，不是卡死了
- 当前主要为技术问题优化。设计、增长、商业等领域也能用，但权威来源
  curation 不如技术领域细致

## Roadmap

- [ ] 可插拔的权威来源配置（按领域）
- [ ] 会话内搜索结果缓存
- [ ] 更多示例，覆盖 AI/ML、运维、设计、增长等不同领域
- [ ] 自动模式的 GIF / video demo
- [ ] 跨 Claude Code 版本的兼容性矩阵
- [ ] Agent Team API 不可用时 `/bp` 的优雅降级

## 贡献

见 **[CONTRIBUTING.md](CONTRIBUTING.md)**。最有价值的贡献是：领域特定的
权威来源、新的红旗模式识别、真实示例输出。

## 作者

Hayes Zhang（[GitHub](https://github.com/hayes-zhang)）。
在微信公众号「**张无常**」写 AI agent 和产品设计（1 万+ 订阅）。

## License

[MIT](LICENSE)——随便用、随便 fork、随便发。只是别拿没验证过的来源
冒充"业界共识"。😉

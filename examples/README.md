# Examples

Real outputs from the `best-practices` plugin, showing what to expect across
all four confidence tiers.

The plugin works for **any** how-to question — technical, product, process,
communication, life skills. Examples below intentionally cover a range of
domains AND a range of tiers (Found / Synthesized / Adapted / Deduced).

## Auto mode (SKILL.md triggers in conversation)

| File | Topic | Domain | Tier(s) shown | Language | What it shows |
|---|---|---|---|---|---|
| [en-auto-mode-nextjs-auth.md](en-auto-mode-nextjs-auth.md) | Next.js 15 auth | Technical | ✅ Found + 💡 Deduced | English | High-confidence Found response with sources and trade-off; side-by-side Deduced fallback |
| [en-auto-mode-adapted-example.md](en-auto-mode-adapted-example.md) | Local-first sync for an AI notes app | Technical (product architecture) | 🧩 **Adapted** | English | **The plugin's most distinctive output**: no direct best practice exists, but Notion / Linear / Obsidian sync engines are adjacent. Three-step adaptation: identify adjacents → extract common principles → customize for AI-agent-in-the-loop scenario, with named domain-shift risks |
| [en-auto-mode-running-1on1s.md](en-auto-mode-running-1on1s.md) | Engineering manager 1:1s | Management / Process | ✅ Found + 💡 Deduced | English | Same flow on a non-technical topic; Deduced fallback for a niche sub-question where even adjacent practices don't help |

## Deep research mode (`/bp` command)

| File | Topic | Domain | Language | What it shows |
|---|---|---|---|---|
| [zh-bp-postgres-indexing.md](zh-bp-postgres-indexing.md) | PostgreSQL 索引设计 | Technical | 中文 | Illustrative `/bp` deep-mode report — multi-tier grading, source attribution, actionable checklist, explicit knowledge gaps |

---

## Contributing examples

PRs welcome — especially **🧩 Adapted-tier examples** (most distinctive output)
and **non-technical examples**. Good examples should:

1. **Be real outputs**, not edited mockups — even with imperfections.
2. **Cover diverse domains**: technical, product, growth, communication,
   life skills, business, parenting, anything.
3. **Show the full range of tiers** — 🧩 Adapted and 💡 Deduced responses
   are as valuable as ✅ Found ones because they demonstrate the plugin's
   honesty when direct best practices don't exist. For 🧩 Adapted examples,
   the three-step adaptation must be visible: adjacent practices named,
   common principles extracted, scenario-specific customization explicit.
4. **Strip personal/project identifiers** before submitting.

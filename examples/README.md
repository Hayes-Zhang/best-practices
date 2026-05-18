# Examples

Real outputs from the `best-practices` plugin, showing what to expect.

The plugin works for **any** how-to question — technical, product, process,
communication, life skills. Examples below intentionally cover a range.

## Auto mode (SKILL.md triggers in conversation)

| File | Topic | Domain | Language | What it shows |
|---|---|---|---|---|
| [en-auto-mode-nextjs-auth.md](en-auto-mode-nextjs-auth.md) | Next.js 15 auth | Technical | English | ✅ Found tier — high-confidence response with sources and trade-off, plus a side-by-side 💡 Deduced fallback for when no authoritative source exists |
| [en-auto-mode-running-1on1s.md](en-auto-mode-running-1on1s.md) | Engineering manager 1:1s | Management / Process | English | Same flow on a non-technical topic — plus an illustration of the 💡 Deduced tier (first-principles fallback) |

## Deep research mode (`/bp` command)

| File | Topic | Domain | Language | What it shows |
|---|---|---|---|---|
| [zh-bp-postgres-indexing.md](zh-bp-postgres-indexing.md) | PostgreSQL 索引设计 | Technical | 中文 | Illustrative `/bp` deep-mode report — 3-tier grading, source attribution, actionable checklist, explicit knowledge gaps |

---

## Contributing examples

PRs welcome — especially **non-technical examples**. Good examples should:

1. **Be real outputs**, not edited mockups — even with imperfections.
2. **Cover diverse domains**: technical, product, growth, communication,
   life skills, business, parenting, anything.
3. **Show both success and failure modes** — Deduced-tier responses (no
   reliable source found, first-principles fallback) are as valuable as
   Found-tier ones because they demonstrate the plugin's honesty.
4. **Strip personal/project identifiers** before submitting.

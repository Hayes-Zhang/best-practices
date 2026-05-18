# Best Practices for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code/plugins)

A Claude Code plugin that **silently checks the web for current best practices**
when you ask technical questions — and refuses to confidently parrot bad sources.

**[中文版 README](README.zh.md)**

---

## Why

LLM training data goes stale fast. Frameworks ship breaking changes, APIs change,
"the recommended way" shifts every six months. When you ask Claude *"how do I do
auth in Next.js?"*, you might get an answer that was correct in 2024 and is now
two versions out of date.

The naive fix — "just always search the web" — has its own failure mode: SEO-optimized
articles, AI-generated filler, and conflicting community posts can confidently mislead
the model in a different direction.

This plugin sits between those failures. It searches when the question warrants it,
**evaluates source quality with structured reverse-thinking**, and grades its own
confidence so you know when to trust the answer and when to dig deeper yourself.

## What it does

```
┌─────────────────────────────────────────────────────────────┐
│ Auto Mode (default)                                         │
│                                                             │
│ Detects technical questions → searches silently             │
│ → applies reverse-thinking quality checks                   │
│ → grades confidence (✅ high / 🔧 medium / ⚠️ low)         │
│ → answers inline with sources & trade-offs                  │
│                                                             │
│ Time budget: ~30 seconds. No command required.              │
└─────────────────────────────────────────────────────────────┘
                          ↓
            (escalate when more depth needed)
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ Deep Mode — /bp "topic"                                     │
│                                                             │
│ 3-agent team (scout / analyst / creator) in parallel,       │
│ Hub-and-Spoke coordination, multi-source cross-validation,  │
│ and a saved Markdown report.                                │
│                                                             │
│ Time budget: 5–10 minutes. Triggered manually.              │
└─────────────────────────────────────────────────────────────┘
```

## Quick start

### Install

```bash
# Add the marketplace
/plugin marketplace add hayes-zhang/best-practices

# Install the plugin
/plugin install best-practices@best-practices
```

### Try it

Ask any framework or version-specific question in a fresh conversation:

> *"How do I set up auth in Next.js 15?"*

The skill will silently search, evaluate, and answer with sources + confidence
tagged at the end. No command needed.

For a deeper multi-method investigation:

```
/bp "Next.js 15 authentication libraries compared"
```

See **[examples/](examples/)** for real outputs.

## When it triggers

The skill is designed to activate automatically — but only when it adds value.

| ✅ Triggers | ❌ Does not trigger |
|---|---|
| Framework/library usage (*"how to use X for Y"*) | Basic concepts (*"what is a closure"*) |
| Comparison/selection (*"X vs Y"*) | Math/algorithms (unchanged for 20 years) |
| Explicit recency (*"latest"*, *"2026"*) | Debugging specific code |
| Version-specific (*"React 19"*, *"Tailwind v4"*) | Creative tasks (writing, naming) |
| Fast-moving domains (AI, frontend, cloud, crypto) | User already specified the approach |
| Niche/recent tools | Meta conversation |
| Explicit keywords (*"best practice"*, *"recommended"*) | |

## How it works

### Three layers of judgment

1. **Trigger detection** — Does this question warrant a search?
2. **Reverse-thinking evaluation** (silent, before responding):
   - Are these sources actually independent, or do they all cite the same origin?
   - Did I search for counter-evidence?
   - Do the suggestions have trade-offs, or is everyone just hyping?
   - How fresh is the information? How fast does this domain move?
3. **Confidence-graded response**:
   - **✅ High** — multiple authoritative sources agree → direct recommendation
   - **🔧 Medium** — fragmented consensus → recommendation flagged as "synthesized"
   - **⚠️ Low** — unreliable sources → *refuses to recommend*, suggests next steps

### Quality red flags

The skill watches for and surfaces:

- 🚩 **Conflict of interest** — source is promoting its own product
- 🚩 **SEO article patterns** — clickbait titles, vague content, no examples
- 🚩 **AI-generated text** — overly generic, no specific lived experience
- 🚩 **Silver-bullet thinking** — complex problem, only one option, no trade-offs
- 🚩 **False independence** — multiple sources tracing back to one origin

## Trust & Safety

This plugin is built around a single principle: **honest source attribution beats
confident guesses**.

- Every recommendation cites real URLs. If a URL isn't real, the plugin won't
  pretend it is.
- Low-confidence responses refuse to give a direct answer. They explain *why*
  they can't, and what you can do next.
- AI-derived suggestions are explicitly labeled "deduced," never disguised as
  "industry consensus."
- The plugin is opinionated about its own limitations.

If you find a case where it's confidently wrong, [open an issue](https://github.com/hayes-zhang/best-practices/issues).
That's the most useful contribution.

## Configuration

| Setting | Default | How to change |
|---|---|---|
| Output directory for `/bp` reports | `./best-practices/` (cwd) | Set env var `BEST_PRACTICES_OUTPUT_DIR` |
| Auto-search time budget | 30 seconds | Edit `skills/best-practices/SKILL.md` |
| Authority sources list | See [authority-sources.md](skills/best-practices/references/authority-sources.md) | Edit the file directly (PRs welcome) |

## Requirements

### Auto mode (default behavior in conversation)

- **Claude Code** with plugin support
- **WebSearch / WebFetch** tools available
- Optional: **Context7 MCP** for first-party framework documentation lookup

### `/bp` deep mode

- All of the above, plus:
- **Agent Team API** tools available in your environment: `TeamCreate`,
  `TeamDelete`, `TaskCreate`, `TaskUpdate`, `TaskGet`, `TaskList`,
  `SendMessage`, and the `Agent` tool with `team_name` + `name` parameters.
  These are required to spawn the 3-agent (scout/analyst/creator) parallel team.
- **Opus model** recommended for the agents (3 instances run in parallel — this
  is the biggest cost driver of `/bp`).
- Sufficient token budget: a typical `/bp` run costs roughly **3–5× the tokens
  of a normal conversation turn**.

> **Note**: If the Agent Team API is unavailable in your environment, `/bp`
> will fail to launch the team. Auto mode will continue to work normally.
> See [issues](https://github.com/hayes-zhang/best-practices/issues) for
> compatibility status.

## Limitations

- The skill's judgment is only as good as the model's. Confidence grades are
  heuristic, not statistical.
- `/bp` consumes ~3–5× the tokens of a normal conversation (3 Opus agents
  in parallel). Use it deliberately.
- Auto-search adds ~10–30 seconds of latency to triggered responses. The first
  sentence ("Let me check the latest approach…") signals this so it doesn't
  feel like the model is stuck.
- Currently optimized for technical questions. Other domains (design, growth,
  business) work but with less domain-specific source curation.

## Roadmap

- [ ] Pluggable authority-source profiles (per-domain)
- [ ] Result caching to skip redundant searches within a session
- [ ] More examples covering AI/ML, devops, design, and growth topics
- [ ] GIF / video demo of auto mode in action
- [ ] Tested compatibility matrix across Claude Code versions
- [ ] Graceful degradation for `/bp` when Agent Team API is unavailable

## Contributing

See **[CONTRIBUTING.md](CONTRIBUTING.md)**. The most valuable contributions are
domain-specific authority sources, red-flag patterns, and real example outputs.

## Author

Built by [Hayes Zhang](https://github.com/hayes-zhang). I write about AI agents
and product design at **张无常** on WeChat (Chinese, 10k+ subscribers).

## License

[MIT](LICENSE) — use it, fork it, ship it. Just don't claim AI consensus you
didn't verify. 😉

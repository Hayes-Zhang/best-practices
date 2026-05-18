# Best Practices for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.1.0-blue.svg)](CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code/plugins)

**Intellectual humility for Claude Code.** When you ask about something you don't
already know well — technical or otherwise — the assistant pauses, looks for what
people have already figured out, and only answers after grading what it found.
If nothing reliable exists, it reasons one out from first principles, explicitly
labeled as deduced rather than found.

**[中文版 README](README.zh.md)**

---

## Why

AI confidently answers from its training data. That's the problem.

- It **doesn't know what it doesn't know**.
- It **doesn't realize someone might have already figured this out**.
- It **doesn't actively look for counter-evidence** before answering.
- When training data is stale or shallow, it **still answers** — just less correctly.

The naive fix — "always search the web" — has its own failure mode: SEO articles,
AI-generated filler, and conflicting community posts can confidently mislead the
model in a different direction.

This plugin sits between those failures. When you ask something you don't already
know well, the assistant:

1. **Searches** for existing best practices on the topic
2. **Evaluates** what it finds with structured reverse-thinking
3. **Grades its own confidence** so you know when to trust the answer
4. **Falls back to first-principles reasoning** when no reliable practice exists —
   never just refusing, never just guessing

Works for any *"how do I do X?"* question — **technical or not**.

## Not just for technical questions

This is what most people miss about the plugin. Yes, it's great for:

> *"How do I do auth in Next.js 15?"*
> *"PostgreSQL indexing best practices for high-write tables?"*

But it's equally useful for:

> *"How do I run an effective 1:1 with a new direct report?"*
> *"What's the best way to negotiate salary at a startup?"*
> *"How should I structure a PR description so it actually gets read?"*
> *"How do other people handle picky-eater toddlers without making mealtimes a battle?"*
> *"What's a reasonable approach to async standup updates for a remote team?"*

The pattern: **anywhere humans have already figured something out and the
recommendation isn't in your head yet**.

## What it does

```
┌─────────────────────────────────────────────────────────────┐
│ Auto Mode (default)                                         │
│                                                             │
│ Detects how-to questions → searches silently                │
│ → applies reverse-thinking quality checks                   │
│ → grades confidence (✅ found / 🔧 synthesized / 💡 deduced) │
│ → answers inline with sources & trade-offs                  │
│                                                             │
│ If no reliable source: derives an approach from first       │
│ principles and labels it as deduced.                        │
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
/plugin marketplace add Hayes-Zhang/best-practices

# Install the plugin
/plugin install best-practices@best-practices
```

### Try it

Ask any how-to question in a fresh conversation. Examples that should trigger:

> *"How do I set up auth in Next.js 15?"* (technical)
> *"How do other teams structure their oncall rotation?"* (process)
> *"What's the best way to write a cold email that gets a response?"* (communication)

You'll see one of three tags appended to the answer:
- **✅ Found** — multiple sources agree, here's what they say
- **🔧 Synthesized** — pieces from several sources, combined
- **💡 Deduced** — nobody seems to have figured this out cleanly, here's a first-principles approach

For a deeper multi-angle investigation:

```
/bp "your topic, technical or otherwise"
```

See **[examples/](examples/)** for real outputs.

## When it triggers

The skill is designed to activate automatically — but only when it adds value.

| ✅ Triggers | ❌ Does not trigger |
|---|---|
| How-to questions (*"how do I X?"*) | Already-executing (*"translate this to Chinese"*) |
| Selection / comparison (*"X vs Y"*) | Basic definitions (*"what is a closure"*) |
| Explicit recency (*"latest"*, *"2026"*) | Math / classical algorithms |
| Version-specific (*"React 19"*) | Debugging specific code |
| Fast-moving domains (AI, frontend, cloud, design systems) | Pure creative tasks (writing, naming) |
| Domain user is new to | User pre-specified the approach |
| Explicit keywords (*"best practice"*, *"recommended"*) | Meta conversation |
| Non-technical how-to (1:1s, hiring, parenting, cooking, etc.) | |

**The cost of an unnecessary 30-second search is small. The cost of confidently
parroting a wrong answer is large. When in doubt, search.**

## How it works

### Three layers of judgment

1. **Trigger detection** — does this question warrant a search?
2. **Reverse-thinking evaluation** (silent, before responding):
   - Are these sources actually independent, or do they all cite the same origin?
   - Did I search for counter-evidence?
   - Do the suggestions have trade-offs, or is everyone just hyping?
   - How fresh is the information? How fast does this domain move?
3. **Tiered response**:
   - **✅ Found** — multiple authoritative sources agree → direct recommendation
   - **🔧 Synthesized** — fragmented consensus → recommendation flagged as "from multiple sources"
   - **💡 Deduced** — no reliable existing practice → first-principles approach + derivation logic, explicitly labeled

### Quality red flags

The skill watches for and surfaces:

- 🚩 **Conflict of interest** — source is promoting its own product
- 🚩 **SEO article patterns** — clickbait titles, vague content, no examples
- 🚩 **AI-generated text** — overly generic, no specific lived experience
- 🚩 **Silver-bullet thinking** — complex problem, only one option, no trade-offs
- 🚩 **False independence** — multiple sources tracing back to one origin

## Trust & Safety

This plugin is built around a single principle: **honest source attribution beats
confident guesses, and honest first-principles reasoning beats confident guesses
too — but only if it's clearly labeled.**

- Every cited URL is real. The plugin won't pretend a URL exists.
- When no reliable sources exist, the plugin doesn't refuse — it switches to
  first-principles reasoning, shows the derivation, and explicitly labels the
  answer as "deduced, not found".
- AI-derived suggestions never disguise themselves as "industry consensus".
- The plugin is opinionated about its own limitations.

If you find a case where it's confidently wrong — or where the confidence tier
is mismatched to reality — [open an issue](https://github.com/Hayes-Zhang/best-practices/issues).
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

## Limitations

- Confidence tiers are heuristic, not statistical. The model's judgment is the
  ceiling.
- `/bp` consumes ~3–5× the tokens of a normal conversation (3 Opus agents in
  parallel). Use it deliberately.
- Auto-search adds ~10–30 seconds of latency to triggered responses. The first
  sentence ("Let me look into the current best practices…") signals this so it
  doesn't feel like the model is stuck.
- Source curation is strongest for technical and product topics. Other domains
  (life skills, parenting, niche hobbies) work equally well — you'll just see a
  different mix of 🔧 and 💡 tiers because the source landscape is different,
  not because the answers are lower quality. 💡 Deduced is a first-class output,
  not a fallback signaling poor work.

## Roadmap

- [ ] Pluggable authority-source profiles (per-domain)
- [ ] Result caching to skip redundant searches within a session
- [ ] More examples covering AI/ML, devops, design, growth, and non-technical domains
- [ ] GIF / video demo of auto mode in action
- [ ] Tested compatibility matrix across Claude Code versions
- [ ] Graceful degradation for `/bp` when Agent Team API is unavailable

## Contributing

See **[CONTRIBUTING.md](CONTRIBUTING.md)**. The most valuable contributions are
domain-specific authority sources, red-flag patterns, and real example outputs
(across diverse domains — non-technical examples especially welcome).

## Author

Built by [Hayes Zhang](https://github.com/Hayes-Zhang). I write about AI agents
and product design at **张无常** on WeChat (Chinese, 10k+ subscribers).

## License

[MIT](LICENSE) — use it, fork it, ship it. Just don't claim AI consensus you
didn't verify. 😉

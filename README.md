# Best Practices for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.2.0-blue.svg)](CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code/plugins)

**Ecosystem-aware Claude Code.** Before answering or coding, the plugin checks
whether someone has already figured out a mature solution for your scenario.

- ✅ **Direct best practice exists** → you get it (with sources and trade-offs)
- 🧩 **Only adjacent scenarios have mature practices** → the plugin adapts them
  (extract the common principle, customize for your scenario)
- 💡 **No analog anywhere** → falls back to first-principles, with the full
  derivation chain visible

Every answer is **tier-labeled** so you know what kind of evidence backs it.
Works for **any** "how do I do X?" question — technical, product, life skills.

**[中文版 README](README.zh.md)**

---

## Why

The most common AI failure mode for `how-do-I-X` questions isn't hallucination —
it's **reinventing the wheel**.

You ask AI to set up multi-LLM access. By default it writes one-by-one API
integrations, when the mature ecosystem solution is **OpenRouter** (5 lines).
You ask AI to add user auth. It hand-rolls JWT + bcrypt, when **Clerk** or
**Auth.js** is what the ecosystem actually uses. You ask AI to add realtime
chat. It builds a WebSocket server from scratch, when **Pusher** or
**Liveblocks** has solved this problem already.

AI knows **how** to do these things. It doesn't know **what the ecosystem has
already converged on** — because mature SaaS solutions get a fraction of the
training-data exposure that hand-rolled tutorials do.

This plugin closes that gap. Before AI rolls its own, it asks: *has the
ecosystem already solved this?* If yes, use it. If only adjacent scenarios
have mature solutions, adapt them. If nothing exists, derive — but say so.

The naive fix — "always search the web" — has its own failure mode: SEO
articles, AI-generated filler, and conflicting community posts can confidently
mislead in a different direction. So this plugin also evaluates what it
finds (5 red-flag detectors) before trusting it.

When you ask something you don't already know well, the assistant:

1. **Searches** for existing best practices on the topic
2. **Evaluates** what it finds with structured reverse-thinking (5 red flags)
3. **Picks the right tier** — ✅ Found / 🔧 Synthesized / 🧩 Adapted / 💡 Deduced
4. **For 🧩 Adapted**: runs a three-step adaptation (adjacent practice → common
   principle → customization for your scenario) — never pretends adapted advice
   is industry consensus
5. **For 💡 Deduced (last resort)**: derives from first principles only when no
   adjacent analog exists, with the full derivation chain visible

Works for any *"how do I do X?"* question — **technical or not**.

## Not just for technical questions

The same "check the ecosystem first" logic works across every domain. Examples
where users would otherwise get hand-rolled answers:

**Technical (ecosystem solution available):**
> *"How do I add multi-LLM access to my app?"* → **OpenRouter**, not one-by-one APIs
> *"How do I add user auth in Next.js?"* → **Clerk / Auth.js**, not hand-rolled JWT
> *"How do I add file upload?"* → **Uploadthing / R2**, not hand-rolled multipart
> *"How do I add realtime?"* → **Pusher / Liveblocks**, not a custom WebSocket server

**Process / communication (proven playbooks exist):**
> *"How do I run an effective 1:1 with a new direct report?"*
> *"What's the best way to negotiate salary at a startup?"*
> *"How should I structure a PR description so it actually gets read?"*

**Life skills (mature practices exist outside dev):**
> *"How do other people handle picky-eater toddlers without making mealtimes a battle?"*
> *"What's a reasonable approach to async standup updates for a remote team?"*

The pattern: **anywhere humans have already figured something out and the
recommendation isn't already in your head**. If a direct answer exists in the
ecosystem, you get it. If only adjacent scenarios have proven playbooks, the
plugin adapts them. If nothing exists, it derives — and labels what it's doing.

## What it does

```
┌──────────────────────────────────────────────────────────────────────┐
│ Auto Mode (default)                                                  │
│                                                                      │
│ Detects how-to questions → searches silently                         │
│ → applies reverse-thinking quality checks (5 red flags)              │
│ → picks one of four tiers:                                           │
│   ✅ Found      direct mature practice (multi-source)                │
│   🔧 Synthesized fragmented but converging same-scenario sources     │
│   🧩 Adapted    no direct practice → adapt from adjacent scenarios   │
│   💡 Deduced    no adjacent analog → first-principles (last resort)  │
│ → answers inline with sources, trade-offs, and tier-label            │
│                                                                      │
│ Time budget: ~30 seconds. No command required.                       │
└──────────────────────────────────────────────────────────────────────┘
                              ↓
                (escalate when more depth needed)
                              ↓
┌──────────────────────────────────────────────────────────────────────┐
│ Deep Mode — /bp "topic"                                              │
│                                                                      │
│ 3-agent team (scout / analyst / creator) in parallel,                │
│ Hub-and-Spoke coordination, multi-source cross-validation,           │
│ explicit Adaptation-candidate vs Deduction-candidate routing,        │
│ and a saved Markdown report.                                         │
│                                                                      │
│ Time budget: 5–10 minutes. Triggered manually.                       │
└──────────────────────────────────────────────────────────────────────┘
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

> *"How do I add multi-LLM access to my app?"* (technical → likely ✅ Found: OpenRouter)
> *"How do other teams structure their oncall rotation?"* (process)
> *"What's the best way to write a cold email that gets a response?"* (communication)

You'll see one of four tier-tags appended to the answer:
- **✅ Found** — multiple independent sources agree on the same scenario
- **🔧 Synthesized** — pieces from several same-scenario sources, combined
- **🧩 Adapted** — no direct practice for this exact scenario, but adjacent
  scenarios with similar constraints have mature practices → three-step
  adaptation (adjacent practice → common principle → customization)
- **💡 Deduced** — no direct AND no adjacent analog → first-principles
  derivation, with the full chain visible (last resort)

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
3. **Four-tier response** (each label is honest about the kind of evidence behind it):
   - **✅ Found** — multiple authoritative sources agree on the same scenario → direct recommendation
   - **🔧 Synthesized** — fragmented same-scenario consensus → recommendation flagged as "from multiple sources"
   - **🧩 Adapted** — no direct practice; adjacent scenarios have mature solutions → three-step adaptation (adjacent practice → common principle → custom adjustment), with named adjacent sources and domain-shift risk warnings
   - **💡 Deduced** — no direct AND no adjacent analog → first-principles with full derivation chain, explicitly labeled (last resort)

### Quality red flags

The skill watches for and surfaces:

- 🚩 **Conflict of interest** — source is promoting its own product
- 🚩 **SEO article patterns** — clickbait titles, vague content, no examples
- 🚩 **AI-generated text** — overly generic, no specific lived experience
- 🚩 **Silver-bullet thinking** — complex problem, only one option, no trade-offs
- 🚩 **False independence** — multiple sources tracing back to one origin

## Trust & Safety

This plugin is built around a single principle: **honest source attribution beats
confident guesses, honest adaptation from adjacent practices beats fabricated
"industry consensus", and honest first-principles reasoning beats both — but only
if every output is clearly tier-labeled.**

- Every cited URL is real. The plugin won't pretend a URL exists.
- When no direct best practice exists, the plugin **first** looks for adjacent
  scenarios with mature solutions and adapts them transparently (Adapted tier),
  with the adjacent source named and domain-shift risks called out.
- When no adjacent analog exists either, the plugin doesn't refuse — it switches
  to first-principles reasoning, shows the derivation, and explicitly labels
  the answer as "deduced, not found".
- AI-derived suggestions (whether 🧩 Adapted or 💡 Deduced) never disguise
  themselves as ✅ Found "industry consensus".
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
  different mix of 🔧 Synthesized, 🧩 Adapted, and 💡 Deduced tiers because the
  source landscape is different, not because the answers are lower quality.
  🧩 Adapted and 💡 Deduced are first-class outputs, not fallbacks signaling
  poor work — they're how the plugin honestly handles "no direct answer exists."

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

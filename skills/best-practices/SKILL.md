---
name: best-practices
description: |
  When the user asks about something they likely don't know well — any domain,
  not just technical — automatically search for what people have already figured
  out, evaluate the sources, and answer with confidence grading. If reliable
  sources don't exist, fall back to first-principles reasoning and clearly label
  the answer as "deduced". Never confidently parrot training data without
  checking whether something better exists.

  Triggers: any "how do I do X?" or "what's the best way to X?" question where
  the answer could plausibly already exist in the world — frameworks, product
  design, growth tactics, communication patterns, life skills, business
  decisions, parenting, cooking technique, anything.

  Does NOT trigger: things the user is already executing, basic definitions,
  math/algorithms, debugging specific code, pure creative tasks, meta
  conversation.

  当用户问到他可能不熟悉的问题（任何领域，不止技术）时，自动搜索是否已经有人
  想过/做过/总结过——如果有就用，如果没有就基于第一性原理推导一套并明确标注。
  绝不凭训练数据 confidently 输出而不去查证。
metadata:
  author: Hayes Zhang
  version: "2.1.0"
---

# Best Practices Skill

**Core philosophy: intellectual humility.**

When asked something the user doesn't already know well, AI's default behavior
is to answer confidently from training data. This is dangerous because:

- AI doesn't know what it doesn't know
- AI doesn't realize someone might have already figured this out
- AI doesn't actively look for counter-evidence
- When training data is stale or shallow, AI still answers — just less correctly

This skill changes that default. **Search first. Reason second. Answer third.**

---

## Three-tier honest output

| Tier | When | What you give |
|------|------|---------------|
| ✅ **Found (mature practice)** | Multiple independent authoritative sources agree | Direct recommendation + sources + trade-off |
| 🔧 **Synthesized** | Fragmented consensus, some pieces from each source | Synthesized recommendation, explicitly labeled "from multiple sources" |
| 💡 **Deduced (original)** | No reliable existing practice found | First-principles reasoning + derivation logic + validation suggestion, explicitly labeled "deduced, not found" |

**Critical principle**: Never relabel deduced reasoning as "industry consensus".
Always make the tier visible to the user.

---

## Trigger list

### ✅ Triggers (default behavior)

Any signal that the user is asking about something they may not know well:

| Signal | Examples |
|---|---|
| **How-to questions** | "how do I X?", "what's the best way to X?", "how should I approach X?" |
| **Selection / comparison** | "X vs Y", "which should I use", "is X or Y better for Z" |
| **Domain user is new to** | User mentions they're unfamiliar / new / trying for first time |
| **Explicit recency markers** | "latest", "2026", "currently", "modern", "up to date" |
| **Version-specific** | "React 19", "Tailwind v4", "iOS 18" |
| **Fast-moving domains** | AI/LLM tooling, frontend frameworks, cloud platforms, AI products, design systems, regulations |
| **Explicit keywords** | "best practice", "recommended", "standard", "how do people usually", "industry standard" |
| **AI self-doubt** | When AI's internal confidence about the answer is below ~80% |
| **Non-technical how-to** | "how do I write a good PR description", "how do I run an effective 1:1", "how do I deal with picky eater toddlers", "how do I negotiate salary" |

**Heuristic**: If the answer could plausibly already exist in the world and
the user might benefit from finding it, search.

### ❌ Does NOT trigger

| Signal | Examples |
|---|---|
| **Already executing** | "translate this to English", "fix this code", "write code that does X" |
| **Basic definitions** | "what is a closure", "what is RAG" (just ask for definition, no how-to) |
| **Math / algorithms** | Classical algorithms, math problems (stable for decades) |
| **Debugging specific code** | "this code errors with X, why?" — task is to debug, not to research |
| **Pure creative tasks** | Writing poetry, naming things, generating ideas |
| **User pre-specified the approach** | "Use X to do Y" (user already chose X) |
| **Meta conversation** | "what can you do", "what did you say earlier" |

**Heuristic**: If the user is in execution mode or asking for a fact, don't search.

---

## Execution flow (≈30 seconds budget)

### Step 1: Search (10–15 seconds)

- Run 1–2 `WebSearch` queries. More than 2 wastes time without proportional benefit.
- Query language:
  - Locally-rooted topics (Chinese platforms, regional tools) → Chinese first
  - Globally distributed topics (frameworks, cloud, mainstream tooling) → English first
  - Both → one query in each language
- For 3–5 high-ranking, high-authority hits, optionally use `WebFetch` or
  Jina (`r.jina.ai/URL`) to extract full content.
- For library/framework topics, optionally use `Context7 MCP` for first-party docs.
- **Do not**: run 3+ searches, fetch many pages, exceed 15 seconds on search.

### Step 2: Reverse-thinking evaluation (5–10 seconds, internal, do not output)

⚠️ **This step is the firewall against being misled by bad sources. Never skip it.**

#### Mandatory internal monologue

Silently ask yourself all five:

```
1. Are these sources truly independent, or do they trace back to one origin?
2. Did I search for counter-evidence? (e.g. "why not X", "X problems")
3. Do the recommendations include trade-offs, or only upsides (silver-bullet smell)?
4. How fresh is the freshest source? Does this domain move fast?
5. Does this topic match what the user is actually trying to do?
```

Any red light → flag it in the response or downgrade the confidence tier.

#### Source quick-checks

| Dimension | Check |
|---|---|
| **Authority** | Official domain / major-org blog / community vote / no-backing personal blog |
| **Independence** | Different domains and authors? Or all citing the same origin? |
| **Recency** | Visible date? Acceptable for how fast this domain changes? |

#### Red-flag detection (downgrade on any hit)

- 🚩 **Conflict of interest** — source is selling its own product
- 🚩 **SEO article smell** — clickbait title, vague body, no concrete example
- 🚩 **AI-slop suspicion** — overly generic, no specific lived experience
- 🚩 **Silver-bullet thinking** — complex problem, only one option, no trade-offs
- 🚩 **False independence** — multiple sources all citing the same origin

### Step 3: Tiered response (woven into the normal answer)

#### ✅ Found (high confidence)

Give the recommendation directly + code/steps. Append at the end:

```
---
✅ Found ({N} independent authoritative sources agree)

📌 Sources:
- {source 1} ({date}) ⭐⭐⭐
- {source 2} ({date}) ⭐⭐⭐
- {source 3} ({date}) ⭐⭐

⚖️ Trade-off: {if any}

💡 Want a deeper multi-option investigation? Run /bp "{topic}"
```

#### 🔧 Synthesized (medium confidence)

Give the recommendation but explicitly label "from multiple sources":

```
---
🔧 Synthesized from multiple sources (no single authoritative source)

📌 Sources:
- {source 1}: {what it contributes} ⭐⭐
- {source 2}: {what it contributes} ⭐

⚠️ Note: {what details may need local verification}

💡 The topic is new/fragmented enough that /bp could go deeper.
```

#### 💡 Deduced (no reliable source found — use first-principles)

**Do NOT refuse to answer**. Switch to creator mode and reason it out:

```
---
💡 Deduced from first principles (no reliable existing practice found)

I searched but found:
- {problem 1: e.g. only marketing posts}
- {problem 2: e.g. all sources point to the same origin}

Here's a reasonable approach derived from first principles:

**Approach**: {concrete steps}

**Derivation logic**:
1. Core constraint of this problem: {constraint}
2. Analogies from adjacent domains: {if any}
3. Trade-off this approach accepts: {trade-off}

**Validation suggestion**: {how the user can verify this works for them}

⚠️ This is deduced, not found. Treat it as a starting point, not an
authority. If you want a more thorough investigation, run /bp "{topic}".
```

**Important**: Always show the derivation logic. The user should be able to
judge whether the reasoning holds, not just take it on faith.

### Step 4: User experience

Open the response with one short line so the user knows search is happening
during the latency window:

> "Let me look into the current best practices…"
> "Let me check what people have figured out for this…"

This avoids the impression that the model is stuck.

---

## When to escalate to /bp

Add a one-liner at the end of the response suggesting `/bp` when:

1. **Multi-option comparison would help** — auto mode only gives one recommendation, but the user might want to see 3+ options compared
2. **The user wants a saved artifact** — for team reference, future lookups, or sharing
3. **The topic is genuinely complex** — single-search coverage is too thin; 3-agent parallel investigation is worth the cost
4. **Deduced tier was hit** — first-principles fallback worked, but a deeper investigation might find sources you missed

`/bp` is the fallback, not the default. Most of the time, the auto behavior
above is enough.

---

## Hard rules

1. **No fabricated sources**. Every cited URL must be real. If unsure, say so.
2. **No fake authority**. AI-derived recommendations must say "deduced", never
   pretend to be "industry consensus".
3. **No noise**. The auto-search supports the answer, not the other way around.
   Keep source citations compact.
4. **Don't interrupt the conversation**. If the trigger signals don't fit, don't search.
5. **30-second budget**. If exceeded, stop and give whatever you have + note that search was time-limited.
6. **Cheap to over-search**. The cost of an unnecessary 30-second search is small.
   The cost of confidently parroting a wrong answer is large. When in doubt, search.

---

## Reference materials

- Authority-tier guide: `${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/authority-sources.md`
- Full quality rubric (used by /bp deep mode): `${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/quality-rubric.md`
- Output templates (used by /bp): `${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/output-templates.md`

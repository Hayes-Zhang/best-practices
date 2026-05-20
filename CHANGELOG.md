# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

_Nothing yet. Contributors: add your changes here before opening a PR._

## [2.2.0] — 2026-05-20

### Changed (positioning + tier system, non-breaking)
- **Repositioned the plugin** from "intellectual humility for Claude Code" to
  **"ecosystem-aware Claude Code"**. The new pitch: before AI rolls its own
  solution, it asks "has the ecosystem already solved this?" Default examples
  in the README now lead with SaaS/integration choices (OpenRouter, Clerk,
  Liveblocks, Uploadthing) — the kind of decision where AI's default
  hand-rolling is most wasteful.
- **Expanded confidence tiers from 3 to 4 levels**:
  - ✅ **Found** — multiple independent authoritative sources agree on the same scenario
  - 🔧 **Synthesized** — fragmented same-scenario consensus
  - 🧩 **Adapted** *(NEW)* — no direct practice; adjacent scenarios with similar
    constraints have mature solutions → three-step adaptation (adjacent practice
    → common principle → customization for current scenario)
  - 💡 **Deduced** — no direct AND no adjacent analog → first-principles (last resort)

  Ordering reflects "directness of evidence." 🧩 Adapted sits between Synthesized
  and Deduced because adapted advice has real-world anchors (each principle traces
  to a validated adjacent practice) but carries domain-shift risk.

### Added
- **🧩 Adapted tier (the headline addition)**: When no direct practice exists,
  the plugin now does NOT jump straight to first-principles. Instead it executes
  a three-step adaptation: (1) identify 2-3 adjacent scenarios with similar
  constraints, (2) extract common principles, (3) customize for the user's
  scenario. Every Adapted recommendation names the adjacent sources and calls
  out domain-shift risks.
- **Agent redesign for Adaptation routing**:
  - `scout`: new **Tier 5 — Adjacent-Domain Mining** in the search strategy.
    Mandatory when Tier 1-3 yields thin results.
  - `analyst`: now classifies each information gap as **🧩 Adaptation candidate**
    (adjacent analog exists) or **💡 Deduction candidate** (no analog). This
    routing tells creator which method to use.
  - `creator`: **Method 0 — Adaptation** is now the priority path. First-principles
    derivation (renamed Method 1) is the fallback, used only when no analog exists.
    `creator` is no longer allowed to default to first-principles when an
    adaptation candidate exists.
- **Quality rubric**: new four-tier decision matrix; new "伪邻近迁移"
  (false-adjacency transfer) red-flag detector for Adapted-tier hygiene.
- **Output templates**: Template B now has a dedicated "三、定制建议 (Adapted)"
  section, separate from "四、原创推荐 (Deduced)". Template A's confidence-tier
  legend covers all four tiers.
- **Authority sources**: new "邻近场景识别策略" (adjacent-scenario identification
  strategy) section, covering constraint-similarity dimension, community-maturity
  checks, and Adapted-specific search keywords.
- **Hard rule in SKILL.md**: "Prefer Adapted over Deduced — never skip
  adaptation when an adjacent analog exists." Skipping Adapted to default to
  Deduced is the most common AI failure mode this plugin guards against.

### Notes
- Public API unchanged. `/bp` and auto-mode behavior are backward-compatible
  with 2.1.0 users — old conversations don't break, just gain a richer fourth
  tier when applicable.
- This is a positioning + tier-system minor (2.1 → 2.2), not a major bump.
  No breaking changes.

## [2.1.0] — 2026-05-19

### Changed (positioning, non-breaking)
- **Repositioned the plugin** from "auto-search for technical questions" to
  "intellectual humility for Claude Code". The same auto-search + reverse-thinking
  flow now applies to any *how-to* question — technical, product, communication,
  process, life skills — anywhere humans have already figured something out.
- **Renamed confidence tiers** for clarity:
  - ⚠️ Low-confidence → 💡 **Deduced** (first-principles fallback)
  - The plugin now **never refuses** to give an answer when no reliable
    sources exist — it switches to first-principles reasoning, shows the
    derivation logic, and labels the answer as "deduced, not found".
- **Trigger checklist broadened** to include non-technical signals:
  management 1:1s, hiring, negotiation, parenting, cooking — anything with
  existing best practices.

### Added
- Non-technical example: [`examples/en-auto-mode-running-1on1s.md`](examples/en-auto-mode-running-1on1s.md)
  covering engineering management 1:1s, including a side-by-side of Found-tier
  and Deduced-tier responses on the same topic.

### Notes
- Public API unchanged. `/bp` works the same way; auto-search behavior is
  refined but not breaking. Hence a minor (2.0 → 2.1), not a major bump.
- Author URL canonicalized to `Hayes-Zhang` (was `hayes-zhang`); both still
  work because GitHub URLs are case-insensitive.

## [2.0.0] — 2026-05-19

### Changed (breaking)
- **Skill behavior redesigned**: The `best-practices` skill now performs lightweight
  searches *automatically* during conversations when it detects technical questions
  with stale-training-data risk. Previously, it only recommended slash commands.
- **`/bp-deep` removed**: Deep research mode is now folded into `/bp` (3-agent
  team is the default behavior of `/bp`). Users only need to remember one command.
- **`/bp` repositioned**: From "quick lookup" to "serious deep research" entry —
  use only when automatic mode is insufficient or you need a saved report.

### Added
- **Reverse-thinking evaluation**: Before answering, the skill silently asks itself
  5 critical questions (source independence, counter-evidence, trade-offs, recency,
  domain volatility) to avoid being misled by SEO-optimized or biased sources.
- **Confidence grading**: Every auto-search response is now tagged ✅ high / 🔧 medium /
  ⚠️ low. Low-confidence answers refuse to give a recommendation and instead suggest
  next steps (run `/bp`, check official sources, consider alternatives).
- **Red-flag detection**: Quick checks for conflict-of-interest, SEO patterns,
  AI-generated content, silver-bullet thinking, and false independence.
- **Trigger checklist**: Clear positive/negative signal lists to ensure the skill
  activates on framework usage, version-specific, comparison, and fast-moving-field
  questions — but stays out of basic concepts, math/algorithms, and debugging.
- **30-second time budget** for auto-search (was implicitly unbounded).

### Removed
- `/bp-deep` slash command (functionality merged into `/bp`).
- Hard-coded path `~/Projects/content/best-practices/` (now defaults to
  `./best-practices/` in the working directory, configurable via
  `BEST_PRACTICES_OUTPUT_DIR`).
- Hard dependency on the personal `web-access` skill.

## [1.0.0] — 2026-04-10

### Added
- Initial release with three-layer architecture:
  - `SKILL.md` for passive detection and command recommendation
  - `/bp` for single-agent quick lookup
  - `/bp-deep` for 3-agent (scout/analyst/creator) deep research
- Quality evaluation framework (authority, consensus, actionability, recency).
- Authority sources reference and output templates.
- Hub-and-Spoke team coordination via Agent Team API.

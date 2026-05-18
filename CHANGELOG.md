# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

_Nothing yet. Contributors: add your changes here before opening a PR._

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

# Contributing to Best Practices

Thanks for considering a contribution. This plugin is small but opinionated —
contributions that **improve its judgment** are especially welcome.

## What's most useful

1. **Authority sources** — Add new high-quality sources for specific domains
   (frameworks, AI tooling, design systems, etc.) by editing
   [`skills/best-practices/references/authority-sources.md`](skills/best-practices/references/authority-sources.md).
2. **Red flags** — Spot a recurring pattern in low-quality sources we don't
   yet detect? Add it to the red-flag list in
   [`SKILL.md`](skills/best-practices/SKILL.md).
3. **Trigger refinements** — Find cases where the skill triggers too often,
   or fails to trigger when it should? Open an issue with the exact prompt.
4. **Example outputs** — Real `/bp` outputs that demonstrate the plugin's
   value are gold. PR them to [`examples/`](examples/).

## What's less useful

- Adding new slash commands. The current 1-command surface is intentional
  (see [CHANGELOG `2.0.0`/`2.1.0`](CHANGELOG.md) for rationale).
- Adding configuration knobs for behavior the skill should decide automatically.
- Generic AI-tool boilerplate (CI for a plugin with no compiled code, etc.).

## How to contribute

1. Fork the repo and create a feature branch.
2. Make your change. Keep edits scoped — one concern per PR.
3. If you're changing the skill's behavior (`SKILL.md` or `commands/bp.md`),
   include a before/after example showing what changes.
4. Update [`CHANGELOG.md`](CHANGELOG.md) under an `## [Unreleased]` section.
5. Open a PR. Describe **what changes for users**, not just what changes in code.

## Testing your changes locally

```bash
# Install your fork as a local plugin
/plugin marketplace add /path/to/your/fork/best-practices
/plugin install best-practices@best-practices-local
```

Then test in a fresh Claude Code conversation:

| Test prompt | Expected behavior |
|---|---|
| "How do I do auth in Next.js 15?" | Auto-trigger, returns answer with sources + confidence |
| "What is a closure?" | No trigger, normal conversational answer |
| `/bp "LLM agent orchestration"` | 3-agent team starts, file saved on completion |

## Code of conduct

Be respectful. Disagree with ideas, not people. We're all here to make this
better, not to win arguments.

## License

By contributing, you agree your contributions are licensed under the
[MIT License](LICENSE).

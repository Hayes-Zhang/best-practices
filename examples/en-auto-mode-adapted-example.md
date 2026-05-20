# Example: Auto Mode — 🧩 Adapted (local-first sync for an AI notes app)

This example shows the plugin's most distinctive behavior: **🧩 Adapted tier**.
No direct best practice exists for the user's exact scenario, but adjacent
scenarios have mature solutions. Instead of jumping to first-principles
(which would produce plausible-sounding but unanchored advice), the plugin
runs a three-step adaptation: identify adjacent practices → extract common
principles → customize for the user's scenario.

---

## User prompt

> I'm building a local-first AI notes app where the user can edit notes
> offline and an AI agent rewrites/expands them in the background. How should
> I design the sync between local edits, AI rewrites, and the server? I want
> conflict-free editing across devices.

## Skill's response

> Let me look into what people have figured out for this…
>
> I searched for "local-first AI notes sync" and similar phrases. There isn't
> a direct best practice for **this exact scenario** (local-first + AI agent
> rewrites in background + multi-device CRDT) — it's too new and combines
> three sub-problems that are usually solved separately. But adjacent scenarios
> with similar constraints have very mature solutions. Here's the adaptation:
>
> ---
>
> 🧩 **Adapted from adjacent practices** (no direct practice for this exact scenario)
>
> I searched for direct best practices and found:
> - Generic local-first articles (Ink & Switch's "Local-first software" essay)
>   give principles but no AI-agent-in-the-loop guidance
> - AI notes apps in marketing posts describe features, not architecture
>
> But found mature practices in adjacent scenarios with similar constraints
> (offline-first + multi-device + structured ops):
>
> **Step 1: Adjacent practices**
>
> - **Notion's local-first migration (2024)** — moved from sync-on-load to
>   SQLite-backed local cache + WebSocket op stream + server-authoritative
>   conflict resolution. Core mechanism: every edit is an operation, not a
>   document diff.
> - **Linear's sync engine (Replicache + their own server)** — operation log
>   replicated to clients via mutators; the server is the source of truth, but
>   clients optimistically apply mutations locally and reconcile on sync.
> - **Obsidian Sync** — file-level diff sync over end-to-end encrypted channels,
>   no central authority required for conflict resolution (CRDT-like behavior
>   via vault metadata).
>
> **Step 2: Common principles (what's invariant)**
>
> - **Operation log, not document diff**: All three encode edits as
>   structured operations (insert-after, replace-range, set-property), not
>   full-document differences. This is what makes conflict resolution
>   tractable across devices and agents.
> - **Server-authoritative reconciliation (with optimistic local apply)**:
>   Both Notion and Linear use the server to break ties but apply edits
>   locally first for instant UI feedback. Obsidian is the exception (no
>   server arbiter), and it works because vaults are typically single-user.
> - **Where they differ**: Notion/Linear assume a server; Obsidian doesn't.
>   The choice depends on whether multi-device-multi-user collaboration is
>   a requirement.
>
> **Step 3: Customize for your scenario**
>
> Your scenario has a fourth actor the adjacents don't have: **an AI agent
> that rewrites content asynchronously**. This is the key adjustment.
>
> - **Adopt the operation-log principle directly** (from all three). Don't
>   try to diff documents — represent both user edits and AI rewrites as
>   operations. This makes AI rewrites first-class concurrent edits, not
>   special cases.
> - **Adopt server-authoritative reconciliation** (from Notion/Linear). You
>   need multi-device, so Obsidian's no-server model won't work.
> - **AI agent treated as a "device"** (this is your customization, not in
>   the adjacents): the AI rewrite agent is just another sync participant
>   with its own client_id. Its rewrites go through the same conflict
>   resolution as a user typing on a phone. This sidesteps "AI vs user"
>   special-casing.
> - **Concrete tech**: Use **Replicache** or **ElectricSQL** (well-documented
>   operation-log sync engines) rather than rolling your own CRDT. Both
>   ship the principles above as a library.
>
> ⚠️ This is Adapted, not Found. Each principle traces to a validated
> mature practice (Notion, Linear, Obsidian), but the combined solution —
> especially AI-agent-as-device — is not directly validated at scale.
>
> Domain-shift risks to watch:
> 1. **AI rewrites can be much larger than user edits**. Linear's mutators
>    assume sub-second operations; an AI agent might apply a 500-token
>    rewrite as a single op. Your conflict-resolution UI needs to handle
>    "a big AI change just landed" gracefully — probably with a diff view.
> 2. **AI agent decisions aren't deterministic**. If two devices both ask
>    AI to rewrite the same passage, you'll get two different results. This
>    isn't in any of the adjacent practices. Decide upfront: is AI rewrite
>    a single coordinator-only op, or do you accept divergent rewrites and
>    let the user pick?
>
> 💡 Want a deeper investigation including comparison of Replicache vs
> ElectricSQL vs Yjs for this specific workload? Run
> `/bp "local-first sync engine for AI-augmented note-taking"`.

---

## What this example demonstrates

1. **No direct answer exists, but the plugin doesn't quit**. The exact
   problem ("local-first AI notes app with background agent rewrites") is
   too new and composite to have a direct best practice. AI's default
   behavior would be to either confidently invent CRDT pseudocode or refuse.
   The plugin does neither — it adapts from adjacents.

2. **Each adapted principle is named, citeable, and traceable**. Notion's
   2024 local-first migration, Linear's sync engine, Obsidian Sync are all
   real, well-documented adjacent practices. The user can verify each anchor.

3. **The customization step is explicit and minimal**. The plugin doesn't
   pretend to "invent" — it adopts adjacent principles directly and adds
   ONE clear, scenario-specific adjustment (AI-agent-as-device). The
   customization is the smallest necessary delta.

4. **Domain-shift risks are surfaced, not hidden**. Two concrete risks
   (large AI rewrites breaking small-op assumptions; AI nondeterminism
   not in any adjacent practice). This is the honesty that makes 🧩
   Adapted trustworthy — without these risks listed, it'd be a
   "borrowed-prestige guess."

## When Adapted beats Deduced

If the plugin had jumped straight to 💡 Deduced ("from first principles, you
need CRDTs and a server arbiter"), the answer would have been:
- ✅ Plausible-sounding
- ❌ Without real-world anchors
- ❌ Ignorant of the well-trodden tradeoffs (Replicache exists, ElectricSQL
  exists, you don't need to invent a CRDT)
- ❌ Unable to surface AI-specific risks (because pure first-principles
  doesn't know what already-shipped systems struggle with)

🧩 Adapted is the plugin's most distinctive output: **"I haven't seen this
exact problem solved, but here's what I'm borrowing from, what I'm changing,
and what could break in the transfer."** That's intellectual honesty without
intellectual paralysis.

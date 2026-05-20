# Example: Auto Mode — Next.js Authentication

This is a real transcript showing how the `best-practices` skill responds **automatically**
to a technical question in conversation, without the user running any command.

---

## User prompt

> Help me set up authentication in a Next.js 15 app.

## Skill's response

> Let me check the latest approach…
>
> For Next.js 15, the current recommendation is **Auth.js (NextAuth v5) with Server Actions**.
> Here's a minimal working setup:
>
> ```bash
> npm install next-auth@beta
> ```
>
> ```typescript
> // auth.ts
> import NextAuth from "next-auth"
> import GitHub from "next-auth/providers/github"
>
> export const { handlers, signIn, signOut, auth } = NextAuth({
>   providers: [GitHub],
> })
> ```
>
> ```typescript
> // app/api/auth/[...nextauth]/route.ts
> export { GET, POST } from "@/auth"
> ```
>
> Use the `auth()` helper inside server components and Server Actions; for client-side
> session access, wrap your app with `<SessionProvider>` from `next-auth/react`.
>
> ---
>
> ✅ **Found** (3 independent authoritative sources agree)
>
> 📌 Sources:
> - [Auth.js v5 docs](https://authjs.dev/getting-started) (updated 2026-02) ⭐⭐⭐
> - [Vercel blog: Auth.js v5 launch](https://vercel.com/blog/auth-js-v5) ⭐⭐⭐
> - [Lee Robinson, Next.js DX lead — talk transcript](https://youtu.be/) ⭐⭐
>
> ⚖️ Trade-off: Auth.js v5 is production-ready but still tagged `beta`. If you need
> the absolute most stable release, v4 still works on Next.js 15 with the Pages Router.
>
> 💡 Want a deeper comparison of Auth.js vs Clerk vs Supabase Auth vs Lucia?
> Run `/bp "Next.js 15 authentication libraries 2026"` for a full multi-agent review.

---

## What this example demonstrates

1. **Automatic triggering**: The user didn't run `/bp`. The skill detected
   a framework-specific question on a fast-moving topic and silently searched.
2. **Body-then-meta structure**: The actionable answer comes first; sources and
   confidence are appended without cluttering the main response.
3. **High-confidence path**: Multiple independent authoritative sources agreed,
   so the skill gave a direct recommendation rather than hedging.
4. **Honest trade-off disclosure**: The `beta` status is surfaced explicitly,
   not buried.
5. **Upgrade hint**: The user can escalate to a full multi-method comparison
   via `/bp` if they need more depth — but they don't have to.

## What a *Deduced* response would look like

If the skill found only SEO-optimized articles and no authoritative source —
**and** no adjacent-scenario mature practice that could be adapted — it
**doesn't refuse**. It switches to first-principles reasoning and labels the
answer clearly. For example, if you asked about a less-mainstream auth scenario
where official docs are thin and even adjacent auth scenarios (passwordless,
hardware-key, federated identity) don't transfer cleanly:

> 💡 **Deduced from first principles** (no direct best practice found, and
> no usable adjacent analog after searching for passwordless / hardware-key /
> federated identity patterns — none of those transferred cleanly to this
> specific case)
>
> I searched but found mostly SEO articles repeating surface-level advice, no
> official documentation for this specific use case, and conflicting community
> posts. I also looked for adjacent auth scenarios with similar constraints,
> but the patterns there made assumptions that don't hold here.
>
> Here's a reasonable approach derived from first principles:
>
> **Approach**: Use Auth.js v5 as the foundation (it's the most flexible
> primitive in the Next.js ecosystem) and implement the custom flow as a
> credentials provider that wraps your specific identity logic. Treat the
> session as opaque from the rest of your app — your custom logic lives
> only inside the provider's `authorize()` callback.
>
> **Derivation logic**:
> 1. Auth in Next.js has two layers: identity verification (who you are)
>    and session management (proof you're still you). They should be
>    separable.
> 2. Auth.js already solves session management cleanly. The unsolved part
>    is your custom identity verification.
> 3. Therefore: keep Auth.js for sessions, customize only the identity
>    layer via a credentials provider.
>
> **Validation suggestion**: Write a 50-line happy-path proof of concept
> before committing to the architecture. If the credentials provider feels
> awkward for your use case, that's signal to look harder for an existing
> pattern rather than push through.
>
> ⚠️ This is deduced, not found. Treat it as a starting point. For deeper
> investigation including alternatives like Lucia, Iron Session, or fully
> custom JWT handling, run `/bp "Next.js custom authentication patterns"`.

This is the **most distinctive behavior** of the plugin: when nobody has
cleanly figured something out, it still gives you a workable answer —
but it never pretends a first-principles guess is consensus.

# Example: Auto Mode — Running Effective 1:1s with a New Direct Report

This example shows the plugin handling a **non-technical** how-to question.
The same auto-search + reverse-thinking + tiered response flow applies
whether the question is about React, PostgreSQL, or running 1:1 meetings.

---

## User prompt

> I just got promoted to engineering manager and have my first 1:1 with my new
> direct report on Monday. How should I structure it?

## Skill's response

> Let me look into what experienced managers actually do for first 1:1s…
>
> The strong consensus for a first 1:1 is to **make it about them, not about
> work**. Your job is to learn who they are, what they care about, and how they
> want to be managed — not to assign tasks.
>
> Here's a structure that shows up across multiple sources:
>
> **1. Set the tone (2 min)**
> - "These 1:1s are your time, not mine. We can talk about anything — work,
>   career, what's frustrating you, what's exciting. The agenda is yours."
>
> **2. Get to know them as a person (10 min)**
> - "Tell me about yourself outside of work."
> - "What's something I should know about you that's not on your LinkedIn?"
>
> **3. Working preferences (10 min)**
> - "How do you like to receive feedback?"
> - "How do you prefer to communicate — Slack, async docs, calls?"
> - "What are signs you're stressed that I should watch for?"
>
> **4. Their goals (5 min)**
> - "Where do you want to be a year from now? Three years?"
> - "What part of this role excites you most?"
>
> **5. Open floor (3 min)**
> - "What questions do you have for me?"
>
> Critically: **bring a notebook and write things down**. It signals you take
> their answers seriously and helps you remember preferences across 1:1s.
>
> ---
>
> ✅ **Found** (4 independent authoritative sources agree)
>
> 📌 Sources:
> - [Lara Hogan — "Questions for our first 1:1"](https://larahogan.me/) ⭐⭐⭐
> - [Camille Fournier — *The Manager's Path* (book)](https://www.oreilly.com/) ⭐⭐⭐
> - [First Round Review — "The Indispensable Document for the Modern Manager"](https://review.firstround.com/) ⭐⭐⭐
> - [Will Larson — "An hour-by-hour first week as a manager"](https://lethain.com/) ⭐⭐
>
> ⚖️ Trade-off: This structure is heavy on getting-to-know-you and light on
> immediate-work. If your team is mid-crisis or your report is anxious about
> a specific work issue, address the work issue first and use this template
> for the second 1:1.
>
> 💡 Want a deeper investigation including 1:1 templates for recurring
> meetings, performance issues, and remote-first teams? Run `/bp "engineering
> management 1:1 best practices"`.

---

## What this example demonstrates

1. **Non-technical domain**: The plugin is just as useful for management,
   communication, and life skills as it is for code.
2. **Strong consensus = direct recommendation**: Multiple recognized
   authorities (Hogan, Fournier, First Round, Larson) converge on the
   same pattern. The skill confidently surfaces it as **✅ Found**.
3. **Honest trade-off**: The recommended structure has a failure mode
   (mid-crisis teams). The skill names it instead of pretending the
   approach is universal.
4. **Source quality matters**: The cited sources are all known names in
   the engineering management space, not random Medium posts. This is
   what the authority grading is for.

## What a deduced response would look like in this domain

If you asked something more niche — say, *"How do I run 1:1s with a direct
report who's twice my age and used to be my peer?"* — the skill might find
fragmented advice or nothing authoritative. In that case (and **only after
trying adaptation** from adjacent scenarios like reverse-mentorship and
peer-to-manager transitions, both of which turned out too generic to anchor
this specific dynamic), the response falls back to Deduced:

> 💡 **Deduced from first principles** (no reliable existing practice found,
> and no usable adjacent analog after searching for reverse-mentorship and
> peer-to-manager transition literature)
>
> I searched but found mostly LinkedIn-influencer posts and conflicting
> Reddit anecdotes — no authoritative source on this specific dynamic. I
> also looked at adjacent scenarios (reverse-mentorship, peer-to-manager
> transition) but those bodies of work treat the age/seniority gap as
> incidental, not central. So no usable adaptation anchor either.
>
> Here's a reasonable approach derived from first principles:
>
> **Approach**: Treat the age/seniority gap as information you should ask
> about directly rather than work around. In the first 1:1, name the dynamic
> explicitly: "I want to acknowledge that this is a transition for both of
> us. You have more experience than I do in many areas. I'd like to talk
> about how you want to use these meetings — what's most helpful for you?"
>
> **Derivation logic**:
> 1. The core risk is unspoken resentment or perceived condescension
> 2. Naming the dynamic transfers control — they get to define how the
>    relationship works
> 3. Asking what *they* want avoids you projecting management theater onto
>    someone who likely doesn't need it
>
> **Validation suggestion**: After the meeting, ask them in Slack: "What
> would make these 1:1s more useful for you?" Their answer (or non-answer)
> tells you whether the framing landed.
>
> ⚠️ This is deduced, not found. Treat it as a starting point, not an
> authority. The dynamics of inverted-seniority management are highly
> individual — your specific report's preferences override any framework.

This is the plugin's most distinctive behavior: **when nobody has cleanly
figured it out, it still gives you something useful — but it never
pretends that something is consensus when it's a first-principles guess.**

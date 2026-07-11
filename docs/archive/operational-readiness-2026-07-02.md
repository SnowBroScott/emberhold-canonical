# Emberhold — Operational Readiness Plan
From "shippable loop" to "survives contact with strangers." Drafted 2026-07-02 (Fable session).

**The reframe this plan is built on:** the feature loop is done. Engine, economy, Vault, Campaigns, Calendar, Lists, onboarding, feed spine — a complete product loop is live today. What's missing is not a module. It's that **Emberhold has never met a stranger.** Every user to date shares a last name with the builder. "Prime-time" means the app survives a family that has no Scott in it. Every phase below closes part of that gap.

**Platform posture:** stays a web PWA. No app store, no Apple review, no payment-rail tax. This is a decision, not a compromise — already validated by the Founding Guildhall architecture (~97% revenue retained via Stripe).

---

## Phase A — Finish what's planned (7/10 + one session)
Already scheduled; confirmed as the correct opener.
- **Avatar Overhaul** (7/10 session) — full-roster pass on the locked style spec.
- **Promote and burn down the 11 hiatus NOTED items**, highest priority:
  - Daily recurring quest date-windowing bug (completed instances leaving the board; missed dailies reset fresh)
  - Edit-quest parity bug (audience selector + monthly recurrence missing from edit form)
  - Adult quest auto-approval (`approved_by` set atomically; embers mint immediately)
  - XP → embers vocabulary kill (sole user-facing currency term)
  - Actor bug (whole-household event attribution in RECENT feed)
- **Kid board IA fix** — directed quests lead; open bounties demote to a secondary pool. A kid's daily surface is currently backwards.
- Founding Guildhall decisions (pricing, border art, free/paid roster split) land here per the locked plan.

**Exit criteria:** roster in-band, known bugs zero, kid board correct.

## Phase B — Stranger-proofing (the phase nobody scheduled)
The hardening pass. Full detail in `emberhold-phase-b-brief-2026-07-02.md` (the Opus handoff brief). Workstreams:
1. **Supabase RLS / tenant-isolation audit** — the one item that can kill the product if skipped. Verify (not assume) that Hold A cannot read, write, or enumerate anything of Hold B's — via the API, not the UI.
2. **Service worker + app shell cache** — makes "installable PWA" true instead of aspirational; prerequisite for push; themed offline state.
3. **Error monitoring + feedback path** — Sentry (or similar) wired in, plus an in-app "something's wrong?" path. Strangers don't screenshot; they leave.
4. **Supabase plan + backup posture** — off free tier (project pausing is a launch-killer), point-in-time recovery confirmed.
5. **Cold-start / empty-state audit** — walk the entire app as a brand-new one-member hold; fix every empty state; one outside family onboards while observed silently.

**Exit criteria:** cross-tenant access provably impossible; offline shows a themed shell; errors report themselves; a stranger can onboard unassisted.

## Phase C — Money and paperwork
- **Founding Guildhall build** — Stripe Checkout + Supabase webhook + household entitlement flag (small build, already spec'd).
- **The operational wrapper:** refund policy, receipt emails (Stripe-native mostly), Stripe tax settings, business-structure decision (sole-prop minimum, ducks in a row before real money moves).
- **Legal / COPPA posture** — a kids' product, so mandatory, but the architecture already did the hard part: kids are Netflix-style sub-profiles with no auth identity, no email, no direct collection. Still required: privacy policy + ToS on theemberhold.com (template-grade fine at this scale), support email, honest pre-auth copy about what's collected.

**Exit criteria:** a stranger can pay, get a receipt, and read a privacy policy that's true.

## Phase D — The funnel
- **Landing page** — theemberhold.com pre-auth stops being a login door and becomes the pitch. The 4-step loop card IS the pitch (parking lot already says lift it). Loop + 3 screenshots + Guildhall offer + "start your hold." Locked pre-auth copy rule applies (imperative "hold," never cold-possessive → "home").
- **Pip-guided Add-to-Home-Screen tutorial** — already fully spec'd (platform-detected, profile screen + Pip's help sheet, suppresses when installed, zero backend). Ship here. Icon-on-homescreen → habit → the entire thesis.
- **Analytics** — currently zero telemetry. PostHog (free tier, privacy-respectable). Six events: hold created, member added, quest created, quest approved, reward redeemed, PWA installed. Without this, Phase E teaches nothing.

**Exit criteria:** a cold visitor can understand, sign up, install, and be counted.

## Phase E — Closed beta (the actual gate)
- 5–10 real families, 2–3 weeks, instrumented. Not people who'll be polite.
- The only question that matters: **does a family with no Scott open the app on day 8?** The thesis (out-habit, don't out-feature) gets tested here for the first time instead of asserted.
- Fix what breaks, watch day-7 retention, then open the doors.

**Exit criteria:** day-7 retention exists and you know what it is.

## Phase F — Post-launch (deliberately fenced)
Wall/display mode, PWA push, Smart Lists v2 (two-zone sort → dictionary → importer), Adventure Log, earning campaigns, couples rail. All real, all good, **all after strangers are in.** Wall mode is a retention feature; retention features come after there's someone to retain. Same muscle as the membrane: keep the launch in the loop that exists.

---

## One-line version
Two sessions to finish planned work → one hard hardening phase → thin money-and-legal plumbing → landing page + analytics → a beta that puts the thesis on trial. **Six to eight working sessions to real families. One skippable-only-at-mortal-peril item: the RLS audit.**

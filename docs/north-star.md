# Emberhold — North Star
The index card taped to the front of the binder. Read this first, cold, at the start of any session.

Last updated: 2026-07-10
Replaces: `emberhold-roadmap.md` (retired — its module-order sequencing is complete; every module it pointed at is built)

---

## What it is, in one line
A gamified household operating system. Chores become **quests** worth **embers**; kids claim and complete them, adults approve, embers buy real rewards at the **Vault**. Mobile-first installable PWA, live at **theemberhold.com**.

## The thesis (the whole bet)
**Out-habit, don't out-feature.**

Google and Apple *can* do it all — which is why they master none of it for families. Their calendars are pure-obligation surfaces: nothing makes you open them, so they drift, so they need a human admin, so they rot. The $400 ambient displays win on one thing only — they're *there*.

Emberhold's game engine is a daily-habit driver. The household's organization stays current **as a side effect of the fun**. The game subsidizes the upkeep. We don't beat Google on features; we out-habit it, and we own the family-specific jobs it does badly.

**This thesis has never been tested.** Every user to date shares a last name with the builder. That's the whole point of the ladder below.

## The three disciplines (what keeps us a master, not a jack)
- **The membrane.** The game lives in **quests only**. Other modules stay clean utilities that may *optionally* spawn a quest. Gamify the chore, not the grocery item.
- **Two backbones.** One currency (**embers** — every feature is a source, a sink, or a pool) and one timeline (**the calendar**). The `+` is the single universal capture point.
- **Role-aware depth.** Kids see a game; adults see operations. Same data, different lens — filters, never separate systems.

**The test for anything new:** does it deepen the daily habit? If not, it's scope creep in a feature costume.

**The calibration check:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that's information.

---

## Where we actually are
**The feature loop is done.** Engine, economy, Vault, Campaigns, Calendar, Lists, onboarding, feed spine — a complete product loop is live today.

What's missing is not a module. It's that **Emberhold has never met a stranger.** "Ready" means the app survives a family that has no Scott in it.

**Platform posture:** stays a web PWA. No app store, no Apple review, no payment-rail tax. A decision, not a compromise — validated by the Founding Guildhall architecture (~97% revenue retained via Stripe). Capacitor path assessed as viable and deliberately deferred.

**Current position:** end of Phase A. The 7/10 session closed roughly a third of the hiatus list, including both items it flagged highest-priority.

---

## The ladder (the only sequence that matters now)
Not module order. **Readiness order.** Each gate has an exit criterion; you don't climb until it's met.

| Gate | What it is | Exit criterion |
|---|---|---|
| **A — Finish what's planned** | Avatar roster transport, hiatus burn-down, kid board IA fix, Guildhall decisions | Roster in-band, known bugs zero, kid board correct |
| **B — Stranger-proofing** | RLS/tenant-isolation audit, service worker, error monitoring, Supabase plan + backups, cold-start audit | Cross-tenant access provably impossible; offline shows a themed shell; errors self-report; a stranger onboards unassisted |
| **C — Money & paperwork** | Founding Guildhall build (Stripe + webhook + entitlement), refund/tax/business posture, COPPA + privacy policy | A stranger can pay, get a receipt, and read a privacy policy that's true |
| **D — The funnel** | Landing page (the 4-step loop card *is* the pitch), Pip-guided install tutorial, PostHog analytics | A cold visitor can understand, sign up, install, and be counted |
| **E — Closed beta** | 5–10 real families, 2–3 weeks, instrumented. Not people who'll be polite. | Day-7 retention exists and you know what it is |

**The one item that is skippable only at mortal peril: the RLS audit.** Everything else is a delay; that one is an extinction event.

**Gate E is the thesis on trial.** The only question that matters: *does a family with no Scott open the app on day 8?* Everything above it is scaffolding to make that question askable.

---

## The fence (real, good, and deliberately after)
Wall/display mode · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface.

All real. All good. **All after strangers are in.**

Display mode is the ambient-presence thesis and it is *load-bearing* — but it is a **retention** feature, and retention features come after there is someone to retain. Same muscle as the membrane: keep the launch inside the loop that already exists.

> **Open tension, flagged not resolved:** display mode is simultaneously "the unproven core bet" and "fenced to post-launch." Both are defensible; they can't both be true forever. If the beta comes back flat on day-8 retention, display mode is the first thing off the fence — it may be the missing half of the thesis rather than a nice-to-have. Scott's call, not a decided thing.

---

## Who owns what (so nobody reads the wrong doc)
| Doc | Its one job |
|---|---|
| **`emberhold-north-star.md`** (this) | Why, and in what order. Read first. |
| **`emberhold-master-spec-2026-06-29-eve.md`** | **What it is.** Canonical design truth. ⚠️ *Currently stale — owes the 7/03 avatar spec fold + Vault dual-mode/audience/couples-rail.* |
| **`emberhold-burndown-tracker-2026-07-10.md`** | **What's left.** The live status board. |
| **`emberhold-build-state-2026-07-10.md`** | **Where the build is.** The save file. |
| **`emberhold-parking-lot-2026-07-10.md`** | **What might be.** Captured, not committed. |
| **`emberhold-operational-readiness-2026-07-02.md`** | The full detail behind the ladder above. |
| **`emberhold-phase-b-brief-2026-07-02.md`** | How to execute Gate B. |
| **`emberhold-qa-harness-2026-07-02.md`** | How QA runs, regardless of which model shows up. |
| **`emberhold-hiatus-wrap-2026-07-09.md`** | Reasoning archive. Not a status board — the burndown superseded that. |

**Drift is the enemy.** When a decision lands, it goes in the spec. When something ships, it goes in the build-state. When it's a maybe, it goes in the parking lot. This doc changes rarely — if it's changing often, something's wrong.

---

## The working model
**Docs are the brain → jAIne writes the Lovable prompt → Scott runs it → screenshots and errors come back → jAIne diagnoses, fixes, updates the spec, writes the next prompt.** Lovable engineers; jAIne translates; **Scott decides.** Each build phase runs as its own chat.

*Source-of-truth upgrade (parked):* version-controlled canonical repo + manifest when Emberhold outgrows Project knowledge. Signal to watch: doc-edit friction — every update is currently a manual file swap.

# Emberhold — North Star
The index card taped to the front of the binder. Read this first, cold, at the start of any session.

Last updated: 2026-07-12

---

## What it is, in one line
A gamified household operating system. Chores become **quests** worth **embers**; kids claim and complete them, adults approve, embers buy real rewards at the **Vault**. Mobile-first installable PWA, live at **theemberhold.com**.

## The thesis (the whole bet)
**Out-habit, don't out-feature.**

Google and Apple *can* do it all — which is why they master none of it for families. Their calendars are pure-obligation surfaces: nothing makes you open them, so they drift, so they need a human admin, so they rot. The $400 ambient displays win on one thing only — they're *there*.

Emberhold's game engine is a daily-habit driver. The household's organization stays current **as a side effect of the fun**. The game subsidizes the upkeep. We don't beat Google on features; we out-habit it, and we own the family-specific jobs it does badly.

**The thesis has not been tested.** But — *corrected 2026-07-12* — the starting line is warmer than this document claimed for two weeks:

> **Thirteen accounts exist, not four.** Scott's guild and close confidants have been using the app for two weeks, signing in as recently as three days ago. **This doc previously stated that every user to date shared a last name with the builder. That was false, and it had been false for a fortnight.**

That changes the shape of Gate E, not the need for it. Friends who signed up because they know Scott are not the same as families who found the product cold. But they are also **not nobody** — they are thirteen people receiving an auth email from a company called *Family-Quest-Board* that lands in their spam folder, which is why that item is no longer filed under polish.

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

**And the policy layer has a hole in it.** See `status.md`. Gate B has teeth.

**Platform posture:** stays a web PWA. No app store, no Apple review, no payment-rail tax. A decision, not a compromise — validated by the Founding Guildhall architecture (~97% revenue retained via Stripe). Capacitor path assessed as viable and deliberately deferred.

---

## The ladder (the only sequence that matters now)
Not module order. **Readiness order.** Each gate has an exit criterion; you don't climb until it's met.

| Gate | What it is | Exit criterion |
|---|---|---|
| **A — Finish what's planned** | Avatar roster transport, hiatus burn-down, Guildhall decisions | Roster in-band, known bugs zero |
| **B — Stranger-proofing** | RLS/tenant-isolation audit, **auth email that isn't from a stranger and doesn't land in spam**, service worker, error monitoring, backup posture, cold-start audit | Cross-tenant access provably impossible; the first email a stranger receives is *from Emberhold* and reaches the inbox; offline shows a themed shell; a stranger onboards unassisted |
| **C — Money & paperwork** | Founding Guildhall build (Stripe + webhook + entitlement), refund/tax/business posture, COPPA + privacy policy | A stranger can pay, get a receipt, and read a privacy policy that's true |
| **D — The funnel** | Landing page (the 4-step loop card *is* the pitch), Pip-guided install tutorial, PostHog analytics | A cold visitor can understand, sign up, install, and be counted |
| **E — Closed beta** | 5–10 real families, 2–3 weeks, instrumented. Not people who'll be polite. | Day-7 retention exists and you know what it is |

**The one item that is skippable only at mortal peril: the RLS audit.** Everything else is a delay; that one is an extinction event.

**Gate E is the thesis on trial.** The only question that matters: *does a family with no Scott open the app on day 8?* Everything above it is scaffolding to make that question askable.

---

## The fence (real, good, and deliberately after)
Wall/display mode · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface.

All real. All good. **All after strangers are in.**

Display mode is the ambient-presence thesis and it is *load-bearing* — but it is a **retention** feature, and retention features come after there is someone to retain.

> **Open tension, flagged not resolved:** display mode is simultaneously "the unproven core bet" and "fenced to post-launch." Both are defensible; they can't both be true forever. If the beta comes back flat on day-8 retention, display mode is the first thing off the fence. Scott's call, not a decided thing.

---

## The lanes (who does what, and where the edges are)

| Lane | Owns |
|---|---|
| **Scott** | Vision, taste, final decisions. **Anything whose success criterion is visual.** |
| **jAIne** | Hears, challenges, translates intent into direction. Build prompts, diagnosis, doc stewardship. **Cannot see. Does not slice images. Does not brief visual work as if she could.** |
| **Lovable** | Engineers. Now the *exception*, not the default — reserved for what Code provably can't do. |
| **Claude Code** | **Text.** Code, config, strings, files, structure — anything it can verify by reading. Reads the codebase. Runs the audits. |

**The edge of Code's lane, found 2026-07-12:** the moment the success criterion is *"does this look right,"* Code is outside its lane — and briefing it harder does not help, because the briefer is blind too. Two blind agents converging confidently is not a review process.

**Model routing:** Haiku for mechanical work · Sonnet for jobs with real latitude · **Opus for the tenant-isolation audit, and only there.** Cost-optimizing the security of your kids' data is the one place the instinct is wrong.

**Scheduling, not budgeting:** design sessions and heavy Code jobs must not share a five-hour window — they contend. The resolution is **temporal separation, never reduced thinking.** The thinking has never been the expensive part. The typing was, and that's what Code is for.

---

## Doc ownership
| Doc | Its one job |
|---|---|
| **`north-star.md`** (this) | Why, and in what order. Read first. |
| **`master-spec.md`** | **What it is.** Canonical design truth. ⚠️ *Owes the avatar spec fold + Vault dual-mode/audience/couples-rail.* |
| **`status.md`** | **Where the build is + what's left.** The single status board. |
| **`decisions.md`** | **What we decided and why.** Append-only. |
| **`parking-lot.md`** | **What might be.** Captured, not committed. |
| **`playbooks/`** | **How to do a thing.** session-protocol · avatar-generation · qa-harness · phase-b-brief. |

**Drift is the enemy.** When a decision lands, it goes in the spec. When something ships, it goes in status. When it's a maybe, it goes in the parking lot.

**And a decision in a doc is not a decision in the product.** The Feast → Hall rename was LOCKED on 2026-07-03 and was still not in the code ten days later. Nothing caught it. *A LOCKED decision with no landing check is a wish.*

---

## The working model
**Docs are the brain → jAIne writes the brief → Code or Lovable builds → screenshots and errors come back → jAIne diagnoses, updates the docs, writes the next brief.** Lovable and Code engineer; jAIne translates; **Scott decides — and Scott is the only one who can see.**

# Emberhold — North Star
The index card taped to the front of the binder. Read this first, cold, at the start of any session.

Last updated: 2026-07-26

---

## What it is, in one line
A gamified household operating system. Chores become **quests** worth **embers**; kids claim and complete them, adults approve, embers buy real rewards at the **Vault**. Mobile-first installable PWA, live at **theemberhold.com**.

## The thesis (the whole bet)
**Out-habit, don't out-feature.**

Google and Apple *can* do it all — which is why they master none of it for families. Their calendars are pure-obligation surfaces: nothing makes you open them, so they drift, so they need a human admin, so they rot. The $400 ambient displays win on one thing only — they're *there*.

Emberhold's game engine is a daily-habit driver. The household's organization stays current **as a side effect of the fun**. The game subsidizes the upkeep. We don't beat Google on features; we out-habit it, and we own the family-specific jobs it does badly.

**The thesis has not been tested.** Thirteen accounts exist — Scott's guild and close confidants. Friends who signed up because they know Scott are not families who found the product cold. But they are **not nobody**: they are thirteen people receiving an auth email from a sender that lands in spam, which is why that item is not filed under polish.

---

## What success is
*The gate ladder used to state a sequence and no destination. This is the destination.*

**Break-even on tooling costs. $636 a year.**

Not growth. Not a business. Emberhold pays for the tools that build it, across Scott's projects, and anything past that is a bonus rather than a target.

**The SMART version: ~27 stranger households buying the $25 Founding Guildhall unlock within twelve months.** Roughly one every other week. A household is a *stranger* household if nobody in it knows Scott.

**Why the number matters more than it looks:**
- **It sets the bar for what "working" means.** 27 households is a number a good week could produce and a bad quarter could miss. It is falsifiable, which is the whole point of writing it down.
- **It changes what the gates are for.** Gate E is not "does this scale." It is *does a family with no Scott in it open the app on day 8.* At 27 households, retention is the only variable that matters and acquisition volume is nearly irrelevant.
- **A one-time SKU funds a one-time year.** Hit $636 and year two needs 27 *more* new households, forever. That arithmetic is why the cosmetic catalog exists as a named revenue stream — a retained household becomes worth $25 + $10 + $5 instead of $25 flat. **The catalog is leverage on retention succeeding, not insurance against acquisition failing.**

**The calibration check, which outranks all of the above:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that's information.

---

## The constitution
**Platform law lives in `master-spec.md` Part I — seven supreme rules. No module may contradict one.** A module may *specialize* within a rule; it may never carve an exception. If a module genuinely needs an exception, the constitution is wrong and gets amended deliberately, once.

**Two of those rules were rewritten on 2026-07-25 and the rewrites matter here:**

- **"One currency" became NO CONVERSION.** Plurality was never the danger; an exchange rate was. A foreign-exchange desk inside a chore app is the failure mode. Two sealed economies sharing a login are not. **Guardrail so this doesn't become four currencies:** a module earns one only with its own distinct earn, its own distinct sink, and no conversion path. Miss any one and what it wants is a counter, not a currency.
- **"Embers mint only on adult approval" became: real-world redeemability requires a second party to mint.** The rule follows convertibility, not effort. A sealed in-app currency needs no second party; a self-logged squat set only cheats the squat. **This is why fitness cannot mint embers** — nobody can verify a set, so minting would either make approval a rubber stamp everywhere including chores, or breach the rule outright.

## Vocabulary — these four nouns are load-bearing
- **HOLD** — the tenant. `families` in schema; always "the Hold" user-facing.
- **SURFACE** — a platform screen every hold gets. Board, Vault, Campaigns, Calendar, Lists, Briefing, wall.
- **MODULE** — an optional functional area a hold may enable. **There is exactly one: fitness.** Stored in `families.enabled_modules`; product name on the glass is **Forge**.
- **REGISTER** — one of Forge · Garden · Keep · Hall. **Aesthetic only** — an avatar class and an ambient theme. Gates nothing. A hold runs mixed registers.

*"Layer" is retired.* It was coined to name four functional areas; three decomposed entirely into quests, lists and campaigns, and the fourth is a module.

## The three disciplines (what keeps us a master, not a jack)
- **The membrane.** The game lives in **quests only**. Other modules and surfaces stay clean utilities that may *optionally* spawn a quest. Gamify the chore, not the grocery item. *(The wall extends this cleanly — it SHOWS and PROPOSES; it never mints, spends, approves, or edits.)*
- **Two backbones.** One timeline (**the calendar**) and one economy (**embers**), with **no conversion** between economies if a second ever exists. The `+` is the single universal capture point.
- **Role-aware depth.** Kids see a game; adults see operations. Same data, different lens — filters, never separate systems.

**The test for anything new:** does it deepen the daily habit? If not, it's scope creep in a feature costume.

**And before promoting anything to architecture: DECOMPOSE IT FIRST.** Name the proposed structure, then list what it would actually be built from. **If the answer is entirely existing surfaces, it is content or theme, not architecture.** Three of four proposed functional areas dissolved under this check in a single session — after a full spec had already been written around them.

---

## Where we actually are
**The feature loop is done, the wall is built, and as of 2026-07-26 activation works.** Engine, economy, Vault, Campaigns, Calendar, Lists, feed spine, the 48-avatar roster, an ambient wall for the home hold — and a five-screen Pip-guided first run that ends with a quest on the board and a reward in the Vault. **A new hold is no longer stranded on an empty screen.**

**The security gate is clean in both directions.** Tenant isolation verified sound under live authenticated attack (07-19). Function grants provably clean (07-21). **Table grants closed 07-26** — `anon` at zero across every table, and the Postgres default that caused six instances of grant drift is fixed at the mechanism rather than the symptom.

**What's missing is not a module. It's that Emberhold has never met a stranger.** "Ready" means the app survives a family that has no Scott in it.

**Platform posture:** stays a web PWA. No app store, no Apple review, no payment-rail tax (~97% retained via Stripe). Capacitor assessed and **DECLINED** with two named reopen triggers: PWA push proving load-bearing at Gate E, or an appliance play after Gate E returns retention driven by the wall.

---

## The ladder (the only sequence that matters now)
Not module order. **Readiness order.** Each gate has an exit criterion; you don't climb until it's met.

| Gate | What it is | Exit criterion | State |
|---|---|---|---|
| **A — Finish what's planned** | Avatar roster transport, hiatus burn-down, Guildhall decisions | Roster in-band, known bugs zero | ✅ **Cleared** |
| **B — Stranger-proofing** | RLS/tenant-isolation audit, grant surface, **auth email that isn't from a stranger and doesn't land in spam**, service worker, error monitoring, backup posture, cold-start | Cross-tenant access provably impossible; the first email a stranger receives is *from Emberhold* and reaches the inbox; offline shows a themed shell; a stranger onboards unassisted | 🟡 **Security half done.** Auth email, service worker and backup posture remain. Cold-start onboarding shipped 07-26. |
| **C — Money & paperwork** | Founding Guildhall build (Stripe + webhook + entitlement), refund/tax/business posture, COPPA + privacy policy | A stranger can pay, get a receipt, and read a privacy policy that's true | ⬜ |
| **D — The funnel** | Landing page, Pip-guided install tutorial, PostHog analytics | A cold visitor can understand, sign up, install, and be counted | ⬜ |
| **E — Closed beta** | 5–10 real families, 2–3 weeks, instrumented. Not people who'll be polite. | Day-7 retention exists and you know what it is | ⬜ |

**The one item skippable only at mortal peril was the RLS audit.** It ran, it breached, it was fixed, it was re-verified. Everything remaining in Gate B is a delay rather than an extinction event — **but the auth email is now load-bearing in a way it wasn't**, because the entire signup path depends on that message arriving.

**Gate E is the thesis on trial.** The only question that matters: *does a family with no Scott open the app on day 8?*

---

## The fence (real, good, and deliberately after)
PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · **the fitness module's Option-B game.**

All real. All good. **All after strangers are in.**

**Two things sit deliberately outside the fence:**
- **The strangers-grade wall.** The own-hold wall is *built* (07-17); what remains fenced is kiosk-hardware deployment and the P4×L8 pass on its write surface. Display mode is a **retention** feature, and retention features come after there is someone to retain. If the beta comes back flat on day 8, this is first off the fence.
- **The fitness module, Option A.** Off the critical path and the best ROI on the board: replacing Fitbod at $15.99/mo is ~$192/yr against the $636 target — roughly a third of break-even with no paywall, no funnel and no Gate E dependency. **A must be a full Fitbod replacement, not a thin MVP.** It blocks nothing and unblocks money.

---

## The lanes (who does what, and where the edges are)

| Lane | Owns |
|---|---|
| **Scott** | Vision, taste, final decisions. **Anything whose success criterion is visual.** |
| **jAIne** | Hears, challenges, translates intent into direction. Build prompts, diagnosis, doc stewardship. **Cannot see. Does not slice images. Does not brief visual work as if she could.** |
| **Lovable** | Engineers. The default build lane for frontend and live-DB work. |
| **Claude Code** | **Text.** Code, config, strings, files, structure — anything it can verify by reading. Reads the codebase. Runs the audits. **Syncs to origin/main before reading.** |

**The edge of Code's lane:** the moment the success criterion is *"does this look right,"* Code is outside its lane — and briefing it harder does not help, because the briefer is blind too.

**Model routing:** Haiku for mechanical work · Sonnet for jobs with real latitude and for synthesis recon · **Opus for the tenant-isolation audit and the jAIne seat.**

**Scheduling, not budgeting:** design sessions and heavy Code jobs must not share a five-hour window. Temporal separation, never reduced thinking.

---

## Doc ownership
| Doc | Its one job |
|---|---|
| **`north-star.md`** (this) | **Why**, in what order, and **what winning is**. Read first. |
| **`master-spec.md`** | **What it is.** Part I is the constitution; Part II is the fitness module. Canonical design truth. |
| **`status.md`** | **Where the build is + what's left.** The single status board. |
| **`decisions.md`** | **What we decided and why.** Append-only. Written for future jAIne, not for Scott. |
| **`parking-lot.md`** | **What might be.** Captured, not committed. |
| **`playbooks/`** | **How to do a thing.** session-protocol · avatar-generation · qa-harness · phase-b-brief. |

**Drift is the enemy, and it runs in both directions.** A LOCKED decision with no landing check is a wish — the Feast→Hall rename sat unshipped for ten days. And shipped work can go undocumented — a full wall shipped while these docs still called it fenced.

**The harder version, learned expensively: STATE LIVES IN THE REPO OR IT DOESN'T EXIST.** On 2026-07-12 the entire shape of first-run onboarding was decided in conversation and never committed. Two weeks later it was rebuilt from a one-line summary in `status.md`, wrong, and Scott had to catch it. **A decision that only exists in a chat log is not a decision. It's a memory, and the next session doesn't have it.**

---

## The working model
**Docs are the brain → jAIne writes the brief → Code or Lovable builds → screenshots and errors come back → jAIne diagnoses, updates the docs, writes the next brief.**

Lovable and Code engineer; jAIne translates; **Scott decides — and Scott is the only one who can see.**

**Two standing rules that keep this honest:**
- **Recon before build, every time — and brief the recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X.
- **"Shipped" means a completed end-to-end loop, verified on the glass.** Not a screen rendering. Not a green checkmark. Not HTTP 200. **A catalog read proves the code is right, never that the behavior is.**

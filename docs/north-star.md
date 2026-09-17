# Emberhold — North Star
The index card taped to the front of the binder. Read this first, cold, at the start of any session.

Last updated: 2026-09-11

---

## What it is, in one line
A gamified household operating system. Chores become **bounties** worth **embers**; kids claim and complete them, adults approve, embers buy real rewards at the **Vault**. Mobile-first installable PWA, live at **theemberhold.com**.

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

⚠️ **ALL $636 NOW RIDES ON STRANGERS, AS OF 2026-09-11.** The fitness module was the one path to break-even that required no funnel, no paywall and no beta — roughly a third of the target in pure cost avoidance. **It is declined.** There is no longer a hedge. **That is the reason Gate E moved ahead of Gate C: if strangers are the only revenue mechanism, find out whether strangers stay before building the machine that charges them.**

**The calibration check, which outranks all of the above:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that's information.

---

## The constitution
**Platform law lives in `master-spec.md` Part I — seven supreme rules. No module may contradict one.** A module may *specialize* within a rule; it may never carve an exception. If a module genuinely needs an exception, the constitution is wrong and gets amended deliberately, once.

**Two of those rules were rewritten on 2026-07-25 and the rewrites matter here:**

- **"One currency" became NO CONVERSION.** Plurality was never the danger; an exchange rate was. A foreign-exchange desk inside a chore app is the failure mode. Two sealed economies sharing a login are not. **Guardrail so this doesn't become four currencies:** a module earns one only with its own distinct earn, its own distinct sink, and no conversion path. Miss any one and what it wants is a counter, not a currency.
- **"Embers mint only on adult approval" became: real-world redeemability requires a second party to mint.** The rule follows convertibility, not effort. A sealed in-app currency needs no second party; a self-logged squat set only cheats the squat.

> ⚠️ **Part II of `master-spec.md` documents the fitness module, which was declined 2026-09-11. It is now history rather than design truth. Do not build from it.**

## Vocabulary — these four nouns are load-bearing
- **HOLD** — the tenant. `families` in schema; always "the Hold" user-facing.
- **SURFACE** — a platform screen every hold gets. Board, Vault, Campaigns, Calendar, Lists, Briefing, wall.
- **MODULE** — an optional functional area a hold may enable. ⚠️ **There are currently ZERO.** Fitness was the only one and it is declined. `families.enabled_modules` exists with no consumer and no candidate. **The noun stays load-bearing because the constitution is written in terms of it.**
- **REGISTER** — one of Forge · Garden · Keep · Hall. **Aesthetic only** — an avatar class and an ambient theme. Gates nothing. A hold runs mixed registers. ⚠️ **"Forge" survives as a register name only. The module that shared the word is dead.**

*"Layer" is retired.*

## The three disciplines (what keeps us a master, not a jack)
- **The membrane.** The game lives in **bounties only**. Other surfaces stay clean utilities that may *optionally* spawn a bounty. **Gamify the chore, not the grocery item.** ✅ **Tested by accident 2026-09-11 and it held: a trip checklist built as bounties cluttered the board, and the same content in Lists was clean. The membrane was right and the discoverability was wrong.** *(**The wall is a full participant, not a display.** It mints, spends and approves. What it does not do is become a second game: no separate economy, no separate rules, nothing that could not happen on a phone.)*
- **Two backbones.** One timeline (**the calendar**) and one economy (**embers**), with **no conversion** between economies if a second ever exists. The `+` is the single universal capture point.
- **Role-aware depth.** Kids see a game; adults see operations. Same data, different lens — filters, never separate systems. ⚠️ **Lists are the one surface with no audience flag at all. Known, deliberate for now, written down so it is not rediscovered as a bug.**

**The test for anything new:** does it deepen the daily habit? If not, it's scope creep in a feature costume.

**And before promoting anything to architecture: DECOMPOSE IT FIRST.** Name the proposed structure, then list what it would actually be built from. **If the answer is entirely existing surfaces, it is content or theme, not architecture.** ✅ **Applied again 2026-09-11: "list import" decomposed to a paste handler. No importer, no integrations, no schema — the clipboard became the API.**

---

## Where we actually are
**The feature loop is done, the wall is built, and as of 2026-07-26 activation works.** Engine, economy, Vault, Campaigns, Calendar, Lists, feed spine, the 48-avatar roster, an ambient wall, and a five-screen Pip-guided first run that ends with a bounty on the board and a reward in the Vault.

**The security gate is clean in both directions.** Tenant isolation verified sound under live authenticated attack (07-19). Function grants provably clean (07-21). Table grants closed 07-26 — `anon` at zero across every table, fixed at the mechanism rather than the symptom.

**As of 2026-08-03 the role vocabulary is Keeper and Kin**, and the own-session architecture is LOCKED with flat or peer holds as its single named reopen trigger.

**As of 2026-09-11 recurring duties remember their own history.** A `series_id` lineage survives roll-forward, so a standing duty can say when it was last done and by whom. **Same-row roll-forward killed the guilt pile in July and silently erased the evidence of a miss along with it; this puts the miss back as one quiet line rather than a wall of red.**

**What's missing is not a module. It's that Emberhold has never met a stranger.** "Ready" means the app survives a family that has no Scott in it.

**Platform posture:** stays a web PWA. No app store, no Apple review, no payment-rail tax (~97% retained via Stripe). Capacitor assessed and **DECLINED** with two named reopen triggers: PWA push proving load-bearing at Gate E, or an appliance play after Gate E returns retention driven by the wall.

---

## The ladder (the only sequence that matters now)
Not module order. **Readiness order.** Each gate has an exit criterion; you don't climb until it's met.

⚠️ **REORDERED 2026-09-11: the beta runs before the money.** A free closed beta needs no Stripe, no refunds and no tax posture, and under own-session the Kin have no accounts, so COPPA exposure is thin. **Building the payment rail first spends fifteen-plus items before asking the only question that matters.**

| Gate | What it is | Exit criterion | State |
|---|---|---|---|
| **A — Finish what's planned** | Avatar roster transport, hiatus burn-down, Guildhall decisions | Roster in-band, known bugs zero | ✅ **Cleared** |
| **B — Stranger-proofing** | RLS/tenant-isolation audit, grant surface, **auth email that isn't from a stranger and doesn't land in spam**, service worker, error monitoring, backup posture, cold-start | Cross-tenant access provably impossible; the first email a stranger receives is *from Emberhold* and reaches the inbox; offline shows a themed shell; a stranger onboards unassisted | 🟡 **Security half done.** Auth email and backup posture remain. |
| **D — The funnel** | **Landing page**, Pip-guided install tutorial, **PostHog analytics** | A cold visitor can understand, sign up, install, and be counted | ⬜ **Promoted.** The landing page doubles as beta recruitment. |
| **E — Closed beta** | 5–10 real families, 2–3 weeks, instrumented. Not people who'll be polite. **Free. Founding households comped for life.** | Day-7 retention exists and you know what it is | ⬜ **Now next.** |
| **C — Money & paperwork** | Founding Guildhall build (Stripe + webhook + entitlement), refund/tax/business posture, COPPA + privacy policy | A stranger can pay, get a receipt, and read a privacy policy that's true | ⬜ **Deferred behind E.** |

**What the beta still needs from C:** a short privacy policy that is true and names `flock.js`. **That is a writing job, not the legal build.**

**The one item skippable only at mortal peril was the RLS audit.** It ran, it breached, it was fixed, it was re-verified. **The auth email is now load-bearing in a way it wasn't** — it was deferred pending DNS spend, and moving the beta forward moved that bill forward with it.

**Gate E is the thesis on trial.** The only question that matters: *does a family with no Scott open the app on day 8?*

---

## The fence (real, good, and deliberately after)
PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · streaks on standing duties.

All real. All good. **All after strangers are in.**

**One thing sits deliberately outside the fence:**
- **The strangers-grade wall.** The own-hold wall is *built* (07-17); what remains fenced is kiosk-hardware deployment and the P4×L8 pass on its write surface. Display mode is a **retention** feature, and retention features come after there is someone to retain. If the beta comes back flat on day 8, this is first off the fence.

⚠️ **THE FITNESS MODULE LEFT THE FENCE BY DYING, NOT BY SHIPPING, 2026-09-11.** It was the best ROI on the board on paper — ~$192/yr of cost avoidance against the $636 target, blocking nothing. **What that framing never priced was the build: an exercise catalog, muscle attribution, a substitution map and a progression engine, to displace a $15.99 subscription that already works.** **Scott's call, on comparative advantage, and it is right.** See `decisions.md`.

---

## The lanes (who does what, and where the edges are)

| Lane | Owns |
|---|---|
| **Scott** | Vision, taste, final decisions. **Anything whose success criterion is visual.** |
| **jAIne** | Hears, challenges, translates intent into direction. Build prompts, diagnosis, doc stewardship. **Cannot see. Does not slice images. Does not brief visual work as if she could.** |
| **Lovable** | Engineers. The default build lane for frontend and live-DB work. |
| **Claude Code** | **Text.** Code, config, strings, files, structure — anything it can verify by reading. Runs the audits. **Syncs to origin/main before reading.** |

**The edge of Code's lane:** the moment the success criterion is *"does this look right,"* Code is outside its lane — and briefing it harder does not help, because the briefer is blind too.

⚠️ **AND THE EDGE OF jAINE'S, LEARNED 2026-09-11: SHE CANNOT SEE WHICH SURFACE A FEATURE LANDED ON EITHER.** A swipe carousel built for the phone Board shipped to the wall, because the brief named the interaction without naming the device. **Name the surface, not just the feature.**

**Model routing:** Haiku for mechanical work · Sonnet for jobs with real latitude and for synthesis recon · **Opus for the tenant-isolation audit and the jAIne seat.**

**Scheduling, not budgeting:** design sessions and heavy Code jobs must not share a five-hour window. Temporal separation, never reduced thinking.

---

## Doc ownership
| Doc | Its one job |
|---|---|
| **`north-star.md`** (this) | **Why**, in what order, and **what winning is**. Read first. |
| **`master-spec.md`** | **What it is.** Part I is the constitution; **Part II documents a declined module and is history.** Canonical design truth. |
| **`status.md`** | **Where the build is + what's left.** The single status board. |
| **`decisions.md`** | **What we decided and why.** Append-only. Written for future jAIne, not for Scott. |
| **`parking-lot.md`** | **What might be.** Captured, not committed. |
| **`playbooks/`** | **How to do a thing.** session-protocol · avatar-generation · qa-harness · phase-b-brief. |

**Drift is the enemy, and it runs in both directions.** A LOCKED decision with no landing check is a wish. And shipped work can go undocumented — a full wall shipped while these docs still called it fenced.

**And the third direction, learned 2026-07-31: TWO CANON DOCS CAN CONTRADICT EACH OTHER AND AN AGENT WILL READ BOTH.** **When two canon docs disagree, the one describing SHIPPED BEHAVIOR wins — and the contradiction is itself a finding to be logged, never silently resolved.**

**The harder version, learned expensively: STATE LIVES IN THE REPO OR IT DOESN'T EXIST.** On 2026-07-12 the entire shape of first-run onboarding was decided in conversation and never committed, then rebuilt two weeks later from a one-line summary, wrong. **A decision that only exists in a chat log is not a decision. It's a memory, and the next session doesn't have it.**

⚠️ **AND A FOURTH, 2026-09-11: A STATUS DOC THAT SHOUTS TRAINS THE READER TO SKIM.** `status.md` had accumulated more warning markers than features, and Scott stopped opening it. **Severity markers are signal only while they are scarce.** The board was slimmed at this session's close; keep it that way.

---

## The working model
**Docs are the brain → jAIne writes the brief → Code or Lovable builds → screenshots and errors come back → jAIne diagnoses, updates the docs, writes the next brief.**

Lovable and Code engineer; jAIne translates; **Scott decides — and Scott is the only one who can see.**

**Three standing rules that keep this honest:**
- **Recon before build, every time — and brief the recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X.
- **PUT A STOP-CLAUSE IN EVERY BRIEF THAT RESTS ON AN UNVERIFIED ASSUMPTION.** ✅ **Four fires, four times right, across three sessions.** On 09-11 it turned a cosmetic date field into a schema fix by refusing to build on a title match.
- **"Shipped" means a completed end-to-end loop, verified on the glass.** Not a screen rendering. Not a green checkmark. Not HTTP 200. **And "verified" must name WHO walked it.** ⚠️ **Never-worked and broke look identical from the glass; ask whether anyone has ever exercised a path before asking what changed.**

**And the correction that keeps recurring: TESTED ON THE GLASS BEATS REASONED FROM A DOC.** Avatar transport sat on the critical path for weeks while working. Last-done was kept off Board cards on a height argument that cost twelve pixels. **When canon describes a shipped state and Scott is available, ask him.**

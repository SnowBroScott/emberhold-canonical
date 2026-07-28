# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-27** — *a design and recon session that shipped no code and right-sized two of the largest items on the board.* **Net code delta: zero. Net recon delta: two Claude Code read-only jobs (`progression.ts` API surface, `append_activity` actor sweep). Net doc delta: status and parking-lot replaced, eight `decisions.md` entries.**

**Production is current.** The 07-25/07-26 frontend was published. The red banner carried at the top of this doc for two sessions is retired — dev and prod are no longer forked.

**The top open security item was downgraded by its own recon.** `append_activity` writes `actor_id` from `auth.uid()` server-side; only `actor_label`, the display string, is client-supplied. The row records who actually acted. **The exposure is a feed that can be made to render a wrong name over a truthful row — cosmetic-layer, not audit-trail corruption.** And the obvious fix is a regression: four of six call sites pass a label that is *deliberately* someone other than the caller, because a parent approving a kid's quest must show the kid's name. Deriving the label from `auth.uid()` would rewrite every completed-quest entry to the approving parent. **Re-scoped from "fix a forgery hole" to "decide what `actor_label` means."**

**The sweep found a real inconsistency instead, and it invalidates a pending check.** Quest approval at the wall is a **direct client-side UPDATE** writing `approved_by: adultId` — the PIN-verified adult — and `enforce_quest_update_authority()` never compares `NEW.approved_by` to `auth.uid()`. Meanwhile `approve_redemption` hardcodes `decided_by = auth.uid()`, the wall's session owner. **The same tap on the same wall records two different people depending on whether it is a quest or a redemption.**

**The fitness module was scoped down and it survived the cut.** Forge is **for the Draper household, not a product.** The Fitbod-replacement standard drops from *beat a funded team* to *Scott and May stop opening Fitbod.* It stays merged inside Emberhold — the 07-25 merge decision was made on infrastructure grounds (no second auth, no second tenancy, one member table, one PIN system) and none of those change based on audience. **`enabled_modules` plus a gated route is the firewall: a stranger household never renders it.**

**Lovable credits are effectively exhausted (12 remaining).** Three candidate builds were considered and all three were talked down or deferred. **Nothing was built this session and that was the correct outcome.**

Last session (prior): **2026-07-26** — the session that closed the oldest security item and shipped activation. Table grants closed, `anon` at zero across all fifteen tables, default privileges fixed at the mechanism. Five-screen Pip-guided first run shipped and cold-walked.

Last session (prior): **2026-07-25 (late)** — the constitution restructure. `master-spec.md` fully regenerated. Registers reverted to aesthetic-only; fitness the sole module.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, and with a working activation path.** Engine, economy, Vault (dual-mode + adult-only rail), Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode (v1 own-hold), the 48-avatar tiered roster with a dormant founder gate, a quest date model deriving household-local time server-side, a tenant-isolation model verified sound under live authenticated attack, a function-grant surface and a table-grant surface that are both provably clean, and a five-screen Pip-guided first run that ends with a quest on the board.

**Emberhold is a ONE-module product with ONE module, now scoped.** The four registers (Forge / Garden / Keep / Hall) are **aesthetic only** — an avatar class and an ambient theme, gating nothing. **Fitness is the sole module, it is not built, and as of 07-27 it is scoped to the Draper household rather than to strangers.**

> **`master-spec.md` owes FOUR folds now** — the onboarding flow, the signup posture, the write-once timezone, and **Part II's rescoping** (household-scoped, the retired Fitbod standard, the dead free/paid split, the engine findings). **Do not regenerate blind; read the current version first.** A dedicated spec pass remains recommended.

> **`north-star.md` is current.** The Forge fence entry still reads accurately — off the critical path, blocks nothing, unblocks money. The $192/yr figure survives the rescoping intact: cost avoidance counts the same as revenue against a tooling-cost target.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 Auth email branding + deliverability.** Sender is `no-reply@auth.lovable.cloud` and lands in spam. **It is the first artifact every stranger receives and the entire signup path depends on it arriving.** Not a Lovable job — DNS at Porkbun plus SMTP (Resend). **Wants an evening, not credits.** | Gate B, Gate D. |
| **2** | **🟡 The three signup glass checks.** Not a build — Scott walking the app. The only unverified work from 07-26. | Gate B honesty. |
| **3** | **The joiner flow has no onboarding at all.** `auth.tsx` routes `create` → the flow and `join` → `/board` directly. Every second adult and every kid arrives cold. **A kid's first run is where day-8 retention actually lives.** A phase, not a prompt — do not start it on a partial credit balance. | Gate D. Activation for everyone who isn't the hold creator. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Off the critical path, teed up and fully designed: the `families.timezone` update path.** Build prompt written 07-27 and not yet run. Bounded, finishable in twelve credits, every design question answered. See OPEN below.

**Off the critical path and now household-scoped: Forge.** Design work is a rest-period activity and costs no build lane. Build work waits for V1.

---

## 🟢 CLOSED / RESOLVED — 2026-07-27

### Publish

- ✅ **The dev/prod fork is closed.** Two days of frontend changes published. Critical path #0 retired.

### Recurrence — the last open verify

- ✅ **Monday reappearance test — CLOSED.** The five weeklies staged at `due_date = 2026-07-27` appeared on the board. **`isActiveQuest` was never the suspect** — the trigger's half was already proven by data and the board's half is now proven on the glass. The recurring-quest lane is fully verified across all three cadences.

### `progression.ts` — read, and better than assumed

*Read-only Code recon. The module survived the 07-25 teardown and had never been read by jAIne.*

- ✅ **`loadableWeights(equipment, inventory)` takes the inventory as a parameter.** This was the question blocking any Forge schema work. **Equipment presets are pure data** — the engine consumes whatever load set it is handed. Nothing to rework.
- ✅ **Barbell snapping already does bounded subset-sum over `plate_pair` rows with `quantity`.** Finite-plate-count search is already implemented. The home-gym case that greedy snapping would break is solved in code.
- ✅ **`"timed"` exists as an `ExerciseEquipmentType`** and returns `null` from `loadableWeights` — "no snapping constraint." Cardio has a hook already.
- ✅ **`estimatedOneRepMax(weightLb, reps, rpe)` is three numbers in, one out.** No equipment parameter, no exercise ID, no storage, no cache. **It cannot contaminate across equipment types** because it carries nothing across calls. Scoping is the caller's job, which is where it belongs.
- ✅ **No bilateral flag and no unit representation — both correct as absences.** Bilateral belongs on the exercise row; unit is `_lb` by naming convention throughout and lbs is now the LOCKED storage unit.
- ⬜ **THE GAP: there is no progression axis except load.** `NextLoad` returns `{loadLb, reason, holdsAfter}`. No reps, no sets, no tempo — **not stubbed, absent.** The engine cannot express *"175 instead of 185, so give me 8 instead of 5."* **That is the feature Scott named as the differentiator and it is also the fix for the 10→15 dumbbell jump.** `consecutive_holds` provides hysteresis, which delays a +50% jump but does not survive one.
- 🔵 **Extending it is a Claude Code job, not Lovable.** Pure TypeScript, ten tests, zero Supabase imports. **Zero credits, desktop-bound.**

### `append_activity` — swept, and the finding is smaller than filed

*Read-only Code recon, briefed to disprove.*

- ✅ **`actor_id` is already server-derived from `auth.uid()`.** The `activity_log` row records who actually acted, and it cannot be faked from the client. `family_id` likewise derives from `current_family_id()`. `_ember_delta` is already nulled server-side for non-parents.
- ✅ **`actor_label` is the only client-supplied identity field in the entire migration set.** The sweep came back with no siblings. `_profile_id` parameters exist on several RPCs but every one identifies a *subject*, not an actor, and each is validated as in-household.
- ✅ **`admit_pending_member` and `deny_pending_member` INSERT directly into `activity_log`** and both stamp `actor_id`/`actor_label` from `auth.uid()` plus a profile lookup. Not a leak.
- 🔵 **THE OBVIOUS FIX IS A REGRESSION.** Four of six `logActivity` call sites pass a label that is deliberately not the caller: `Briefing.tsx` and `quest.$id.tsx` pass `claimed_by ?? assigned_to`; `vault.tsx`'s approve path passes `r.requested_by`. **A parent approves and the feed correctly names the kid.** Deriving the label from `auth.uid()` would rewrite every completed-quest entry to the approving parent's name — a regression on the primary feed surface.
- 🔵 **Severity re-rated.** Under the walk-up trust boundary this was never a cross-tenant concern. It is *could a kid in your own house make the feed render a wrong name*, over a row that still records the truth. **Real, worth cleaning, not urgent.**
- ⬜ **Re-scoped as a design question, not a bug fix:** what does `actor_label` mean? The likely shape is a validated subject ID with the label derived server-side from it — a third field, not a substitution. **Needs Scott, not a migration.**

### Forge — scoped

- ✅ **Forge is scoped to the Draper household, not to strangers.** The fitness app market is saturated with competent products; the bar for a stranger-facing Forge is far higher than Emberhold's, and Emberhold's thin market is exactly what makes *it* worth shipping. **Forge stays merged** — the merge was decided on infrastructure grounds and a two-user audience makes standalone *worse*, not better, since the infrastructure cost stays fixed while the audience shrinks to two.
- ✅ **The "FULL Fitbod replacement" standard is retired.** Read literally it has no exit — Fitbod has a team. Read as a user test — *Scott and May stop opening Fitbod* — it is achievable, with one known gym and no edge cases to serve.
- ✅ **The free/paid split no longer applies to Forge.** Nobody buys an LLM tier in a two-person app. Constitutional rule 7 is unengaged here.
- ✅ **Dilution risk is structurally impossible.** `enabled_modules` plus a gated route with no nav entry means a stranger household never renders Forge.
- 🔵 **The real risk is SUBSTITUTION, not complexity.** A Forge session happening *instead of* a Gate B item rather than alongside it. **That is the tripwire.**
- 🔵 **Forge design is a rest-period activity; Forge building waits for V1.** Design costs no build lane and competes with nothing.

### Gym inventory — captured

Cardio: treadmill, elliptical, stationary bike, battle ropes, Meta Quest. Bands: full set, light → ultra heavy. Free weights: dumbbells 8, 10, then 5lb increments to 55 · EZ-curl bar · kettlebells to 35 · bench rack with 30 and 45lb bars · Smith machine (assumed 20lb bar, 0 offset — **unverified, physical check owed**) · cable rack, high and low pulleys · pull-up bar · tricep dip stand. Plates: pairs of 2.5, 5, 45 · six 10s · six 25s.

- 🔵 **Recorded as an override profile, not as the model.** Standards-plus-override is the LOCKED approach; plate counts turn out not to matter under it.

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE THREE SIGNUP CHECKS — still the only unverified work from 07-26.** Production is now current, so these are unblocked.
  1. **Cold signup, create path, tapping the email link on a phone.** The case broken from the start — Mail's in-app webview is a different storage partition. Confirm it asks once and only once.
  2. **Cold signup, join path.** No PIN field, no error toast at the end, lands as pending.
  3. **Original-tab path.** Sign up, ignore the email, press "I confirmed," sign in. **Should land on finish-setup identically to path 1** — the proof the fork is closed rather than moved.
- ⚠️ **Wall adult-verified turn-in — THE EXPECTATION WAS WRONG AND IS CORRECTED.** This item previously read "`approved_by` lands as session-owner." **The code writes the PIN-verified adult (`adultId`), not the session owner.** Test against the corrected expectation, and see the inconsistency logged under OPEN. Still verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits and balance moves.
- 🟡 **Monthly post-fix is technically unexercised.** All four monthly approvals predate the timezone fix. Failure window is approving on the last evening of a month after UTC has rolled. Narrow. Note it, don't wait on it.
- 🟡 **STALE chip predicate.** Likely `due_date < today`, which the two stranded past-due weeklies would fully explain. Confirm, then fold or leave deliberately.
- 🟡 **Grant-revoke verification probe job — BRIEF DRAFTED, DEFERRED TWICE.** `SQLSTATE 42501` = broken grant (FAIL); any application-level error = the call reached the function body (PASS). *Sonnet · auto-accept OFF · read-only.* Scope includes the `families` anon revoke and the 07-26 table revoke.
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built, not before. **The trigger has NOT fired — Forge is designed, not built.** Do not re-litigate, do not delete.
- 🟡 **The ember progress trail is a visual success criterion and only Scott can judge it.**
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, **check the wall specifically.**
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. **Now also the test case for the timezone heal.**
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — new from 2026-07-27

- ⬜ **🔴 `approved_by` vs `decided_by` — the wall records two different people for the same tap.** Quest approval is a **direct client-side UPDATE** from `wall.tsx:522` writing `approved_by: adultId` (the PIN-verified adult). `enforce_quest_update_authority()` gates it on `has_role(auth.uid(), 'parent')` but **never compares `NEW.approved_by` to `auth.uid()`** — so any parent session can stamp any adult. `approve_redemption` by contrast hardcodes `decided_by = auth.uid()` (the wall's session owner), and its caller accepts an `approverId` it never sends. **Not a forgery risk under the walk-up boundary — an inconsistency, and a design question about which one is right.** Quest approval's behavior is arguably the *better* one for a shared wall; redemption's is the safer one. **Decide which, then make them agree.**
- ⬜ **The `families.timezone` update path — DESIGNED, PROMPT WRITTEN, NOT RUN.** Adds `families.timezone_confirmed_at timestamptz NULL`, a parent-gated `set_household_timezone(_timezone text)` SECURITY DEFINER RPC validating against `pg_timezone_names`, a **silent one-time heal** on authenticated load when `timezone_confirmed_at IS NULL` and the member is a parent, and a settings display plus reset button calling the same RPC. **No table-level UPDATE grant on `families`** — the 07-15 privilege-escalation fix stays intact. Bounded, finishable in twelve credits.
- ⬜ **`progression.ts` has no non-load progression axis.** See CLOSED above. **The Forge differentiator is unbuilt in the one asset that already existed.** Claude Code job, zero credits.
- ⬜ **The Smith machine offset is assumed, not measured.** Working assumption: 20lb bar, zero counterbalance. **A wrong offset silently corrupts every Smith e1RM forever, unreconstructably.** Physical check.

## ⬜ OPEN — carried

- ⬜ **`enabled_modules` has no consumer.** Fifth session carrying it. **It now has a named future consumer** — the gated `/forge` route — but Forge is not built, so it remains unread. Wire it when Forge ships or delete it.
- ⬜ **`master-spec.md` owes FOUR folds.** Onboarding flow, signup posture, write-once timezone, **Part II rescoping.** Read before regenerating.
- ⬜ **Unapproved weekly and monthly quests never roll forward.** `roll_missed_dailies()` filters `recurrence='daily'`; weekly/monthly successors are minted only by `handle_quest_approval()`. **Two stranded right now** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted). A guilt pile across two of three cadences.
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor. Wrong reward for being early.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY (07-26).** Approval is the real gate. **What changes is the curriculum:** onboarding never teaches quest authoring to a non-approver. Absence, not a locked door. *(Open recon, one line: is quest creation actually discoverable from a kid's board, or merely reachable?)*
- ⬜ **An established hold can land on `/onboarding/first-quest` by URL** and be walked through cold-start copy. `PipHelp`'s "Replay the intro" must never point at it without a read-only mode.
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question, not a project.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag, not a role check. Do not build now.
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** Logged so it is not rediscovered as an oversight.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** Load-bearing as a diagnostic surface. Decide deliberately.
- ⬜ **Module navigation is unresolved.** Seven tabs is already one past mobile comfort. **Not "add another tab."** **Forge's contained-route posture defers this entirely** — it comes due at flip time, not before.
- ⬜ **Feed verb drift — CONFIRMED LIVE 07-26.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on QUEST POSTED.

---

## 🟢 SECURITY TRIAGE — statuses as of 2026-07-27

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated 07-21 late, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** Parent-role + same-household gated; anon denied. *(Minor: `confirm()` copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`. Re-confirmed three times.
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.** Intra-household. **Revisit if the own-session-vs-per-member-auth fork is decided.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** `wall_request_redemption` stores `requested_by = _profile_id` (the subject) with no record of the invoking session. **Confirmed on 07-27 from the function source.** Same revisit trigger.
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.** Structurally unclearable.

**Fixed:**
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable — FIXED 07-19.**
- ✅ **`anon` full CRUD on `families` incl. `is_founder` — FIXED 07-25.**
- ✅ **`anon` full CRUD on the other fourteen tables — FIXED 07-26.** Plus TRUNCATE/TRIGGER/REFERENCES/MAINTAIN stripped from `authenticated`, and default privileges fixed at the mechanism.
- ✅ **Adult PIN plaintext in `localStorage` — CLOSED BY DELETION 07-26.**

**Real, open:**
- ⬜ **`actor_label` display forgery — DOWNGRADED 07-27, RE-SCOPED AS DESIGN.** `actor_id` is server-derived and truthful; only the rendered name is client-supplied. **No longer the top open security item.** See CLOSED above for why the obvious fix is a regression.
- ⬜ **`approved_by` accepts any adult from any parent session** — see OPEN. Inconsistent with `decided_by`. Design call first.
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together in one audience-read pass**, *with* the own-session-vs-per-member-auth decision. **These are now the top open security items by default.**
- ⬜ **`supabase_admin` default-privilege residual** — unreachable from this connection, platform-scoped. Logged as a known limit.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. Low priority.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **🔴 Auth email branding + deliverability.** Critical path #1. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Grant-revoke verification probe job** — see PENDING VERIFY.
- ⬜ **Ask Lovable what `sandbox_exec` is.** Recon, not alarm.
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true. **Note: Forge would promote this from polish to load-bearing**, since a garage has unreliable connectivity — but Forge is not built, so it stays a Gate B item at its current priority.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users; `QA Parent`/`QA Joiner`/`QA Verify Hold`; `+ca@gmail.com`/"Testies"; the 07-21 test weeklies; the ghost "Take out the trash" successor; the 07-22/23 pre-fix dailies; the several throwaway holds from 07-26. **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The joiner flow has no onboarding.** CRITICAL PATH #3.
- ⬜ **`families.timezone` has no update path.** Designed and prompted 07-27, not run.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **Feed verb drift** · **Recurrence chip legibility — must read `Monthly · 1st` / `Weekly · Mon`** · **The early-approval seam** · **Onboarding screenshots for screen 3** *(manual capture; possibly a Claude Code drop-and-point job rather than a Lovable build — worth testing before spending a credit)* · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** *(de-prioritized)* · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6) · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED 07-22) · flat/peer holds · scripted screenshot capture (DECLINED 07-26) · the "how Scott & jAIne work" collaboration profile · **the timezone nudge (30-day disagreement signal)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19, re-affirmed 07-21 and 07-27)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are now ignored or downgraded *because of* this boundary** — the PIN lock, on-behalf redemption, and as of 07-27 the `actor_label` display forgery. Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT. (NEW 07-27.)** The `append_activity` sweep was briefed to disprove and it disproved the item jAIne had ranked top of the board — including the fix jAIne had already described. **A recon that only ever confirms is a recon briefed wrong.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX. (NEW 07-27.)** Scott asked what the security lane was actually solving, in plain language, and the item collapsed under the answer. The same question applied to the timezone edit path shrank it from a Gate B build to a one-time heal. **Two of the largest items on the board were right-sized by a question, not by work.**
- **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.** "Top open security item" was carried across three sessions on a summary nobody re-derived. **Re-read the reasoning, not the ranking.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE. (07-26.)** RLS covers SELECT/INSERT/UPDATE/DELETE. It does not cover TRUNCATE, TRIGGER, REFERENCES or MAINTAIN.
- **FIX THE MECHANISM, NOT THE INSTANCE. (07-26.)** Six grant-drift instances were one Postgres default re-asserting itself.
- **BUILD THE FRAME BEFORE THE CONTENT. (07-26.)**
- **A REFACTOR THAT LOOKS IDENTICAL IS A SUCCESSFUL REFACTOR. (07-26.)**
- **DECOMPOSE BEFORE YOU PROMOTE. (07-25 late.)** If a proposed structure is built entirely from existing surfaces, it is content or theme, not architecture.
- **Fetch the canon before producing anything. (07-25 early.)**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.**
- **jAIne must not prescribe a check the frontend cannot perform. (07-26.)**
- **State a hypothesis as a hypothesis, then read before asserting. (07-26, and again 07-27.)** jAIne invented a wife's name and asserted it as fact, and asserted a repo-capture leak in a workflow Scott has been reliably running. **Both were pattern-completion presented as knowledge.**
- **An OID heuristic dates objects relative to each other, never absolutely.**
- **A code revert is not a database revert.** Undo schema forward, always.
- **The docs are not the live codebase — and can diverge silently.** Code recon syncs to `origin/main` first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.**
- **Sweep the class before fixing the instance.**
- **A defensive layer is not verified by the migration that adds it.** **HTTP 200 is not "renders."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (the tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering. (07-26.)** Put the write in its own prompt whenever the reads are what determine whether the write is safe.
- **SESSION LANE, DECLARED AT OPEN. (NEW 07-27.)** Four values: **design-only · design + Lovable · design + Code · full.** Lovable is a browser tab and runs from a phone; **Claude Code requires a desktop.** Credits and hands are independent switches. jAIne briefs only into the open lane — no build prompts into an empty credit balance, no Code recon at someone standing between sets.

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-26** — the table-grant read closed; `anon` at zero across fifteen tables; default privileges fixed at the mechanism. Activation shipped: five-screen Pip-guided first run, cold-walked end to end. Signup rebuilt; the `pending_setup` stash and its plaintext PIN deleted. Household timezone capture shipped inside `complete_household_setup`.
- **2026-07-25 (late)** — the constitution restructure. `master-spec.md` fully regenerated. Registers reverted to aesthetic-only; fitness the sole module. Two constitutional rules rewritten.
- **2026-07-25 (early)** — the module reframe. An eight-table parallel tenancy built and dropped the same night. `src/lib/progression.ts` the only survivor.
- **2026-07-23** — the household-local date seam. `families.timezone` + `household_today()`; six `CURRENT_DATE` violations across two functions.
- **2026-07-21 (late)** — the SECURITY DEFINER grant surface. Seven functions carrying `PUBLIC` + `anon`.
- **2026-07-21 (evening)** — recurring-quest visibility. `isActiveQuest` unified into `quest-helpers.ts`.
- **2026-07-19 (audit)** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.
- **2026-07-19 / 07-18** — wall display-mode fine-tune on physical glass. Avatar transport end-to-end.
- **2026-07-16** — roster "no members" root-caused to zero Data-API grants. Pending→admission routing fixed.
- **2026-07-15** — admit-on-approval shipped. Live privilege-escalation in `complete_household_setup` fixed.
- **2026-07-14** — admit-on-approval data layer. Recurrence reworked to fixed calendar anchors.
- **2026-07-12** — Claude Code proven as a build lane. Auth hardening. 13 real accounts confirmed.
- **2026-07-11** — engine daily respawn verified. Avatar roster regenerated (48 characters, 19 sheets).
- **2026-07-10** — engine auto-approval, edit-form parity. XP killed. Vault kid/adult/couples-rail.
- **2026-07-03 → 06-26** — Avatar Overhaul designed (Feast→Hall). Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, Campaigns, Calendar, the Vault, PIN, Quest Log.

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-26** — *the session that closed the oldest security item on the board and shipped activation.* **Net code delta: two Lovable builds (setup shell + four onboarding screens), one auth rework, three migrations. Net doc delta: status and parking-lot replaced, thirteen `decisions.md` entries.**

**The top security item is CLOSED after three sessions of carrying it.** The table-grant read finally ran. `anon` held full CRUD — including TRUNCATE — on fourteen of fifteen tables in `public`; only `families` was clean, because it was the only one anyone had ever revoked. **RLS was holding** (all 15 enabled, four anon-reachable policies, all RESTRICTIVE `false` denials, nothing permissive), so this was defense-in-depth rather than a live hole — **but RLS gates only SELECT/INSERT/UPDATE/DELETE. TRUNCATE, TRIGGER, REFERENCES and MAINTAIN were ungated entirely.** All revoked. `authenticated` reduced to exactly its four verbs, nine column-level grants byte-identical, verified from `pg_class.relacl` rather than from the migration.

**And the mechanism was fixed, not just the symptom.** Default privileges for the `postgres` owner no longer auto-grant `anon` on new objects. Six instances of grant drift were the same Postgres default re-asserting itself; that path is now closed. A `supabase_admin` residual remains, unreachable from this connection, firing only for objects the platform itself creates. **Grant drift downgrades from recurring defect to bounded residual.**

**Activation shipped.** The top structural blocker since 07-12 — onboarding ending at setup and stranding the user on an empty board — is closed. A new hold now walks five Pip-guided screens (`add-family` → `first-quest` → `stock-vault` → `the-hold` → `handoff`) and lands on a board carrying its own quest and its own reward. **Cold walk verified end to end on the glass. Per-screen writes verified** — skipping at screen 2 preserved screen 1's quest.

**Signup was rebuilt and it is the one thing tonight that is NOT verified.** Pre-verification collection of name, avatar, role, PIN, hold name and create-vs-join is deleted; the initial screen is email and password only. The `emberhold.pending_setup` localStorage stash is gone. **Three glass checks owed — see PENDING VERIFY.**

> **🔴 PRODUCTION IS OLD FRONTEND AGAINST A NEW DATABASE.** Nothing from 2026-07-25 or 07-26 has been published. Migrations apply live; frontend sits in dev. This is survivable *only* because `_timezone` was added to `complete_household_setup` with `DEFAULT NULL`, so the old frontend's call still resolves. **PUBLISH IS THE FIRST ACTION NEXT SESSION.**

Last session (prior): **2026-07-25 (late)** — a pure documentation session. `master-spec.md` fully regenerated (the four overdue folds), the constitution restructure, ten `decisions.md` entries. The four registers reverted to aesthetic-only; **fitness is the sole module.** Two constitutional rules rewritten: "one currency" → **no conversion**, and verification-minting rescoped to **approval authority**.

Last session (prior): **2026-07-25 (early)** — the module reframe. An entire parallel tenancy built and dropped the same night. **`anon` found holding full CRUD on `families` including `is_founder`** — grant-drift instance six, and the reason the table-grant read was re-priced to front-of-queue.

Last session (prior): **2026-07-23** — the household-local date seam. `families.timezone` + `household_today()` replaced six `CURRENT_DATE` violations across two functions.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, with a working activation path for the first time.** Engine, economy, Vault (dual-mode + adult-only rail), Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode (v1 own-hold), the 48-avatar tiered roster with a dormant founder gate, a quest date model deriving household-local time server-side, a tenant-isolation model verified sound under live authenticated attack, **a function-grant surface AND a table-grant surface that are both provably clean**, and **a five-screen Pip-guided first run that ends with a quest on the board.**

**Emberhold is a ONE-module product with ONE optional module candidate.** The four registers (Forge / Garden / Keep / Hall) are **aesthetic only** — an avatar class and an ambient theme, gating nothing. **Fitness is the sole module**, it is not built, and **its shape is an open decision** (Option A: tool only. Option B: tool plus one sealed game. **A ships before B is designed.**).

> **`families.enabled_modules` is correctly named as shipped and needs no migration.** `['household']` = base; `['household','fitness']` = base + Forge. **The one row carrying the retired `'training'` value (The W Drapers) was corrected to `{household}` on 07-26.** Deliberately not set to `{household,fitness}` — the module does not exist, and a row should not claim an enablement with nothing behind it.

> **`north-star.md` was regenerated 2026-07-26 and is current.** It now carries the constitution restructure, the module architecture, the two rewritten rules, and — for the first time — **the success definition and the $636 target**, which the gate ladder had been missing. The four-session carry is closed.

> **`master-spec.md` owes three folds from 07-26** — the onboarding flow, the signup posture, and the write-once timezone. **Do not regenerate blind; read the current version first.** A dedicated spec pass is recommended as the first work of the next session, after publishing.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **0** | **🔴 PUBLISH.** Two days of frontend changes sit in dev against a live-migrated database. Everything below is downstream. | Everything. |
| **1** | **✅ TABLE-GRANT READ — CLOSED 2026-07-26.** `anon` at zero on all 15 tables, `authenticated` at four verbs, default privileges fixed, verified from `relacl`, logged-out path confirmed on glass. Function grants closed 07-21. **The security gate is now clean in both directions.** | — |
| **2** | **✅ ACTIVATION — SHIPPED 2026-07-26.** Five-screen Pip-guided first run. Cold walk verified. **Timezone capture shipped inside `complete_household_setup`.** | — |
| **3** | **🟡 Signup rework — SHIPPED, NOT VERIFIED.** Three glass checks owed. | Gate B honesty. |
| **4** | **🔴 Auth email branding + deliverability.** Now the top unstarted item. Sender is `no-reply@auth.lovable.cloud` and lands in spam. **It is the first artifact a stranger receives, and the entire signup path now depends on them actually getting that email.** | Gate B, Gate D. |
| **5** | **The joiner flow has no onboarding at all.** `auth.tsx` routes `create` → the flow and `join` → `/board` directly. Every second adult and every kid arrives cold. **Arguably higher-value than the creator flow** — a kid's first run is where day-8 retention actually lives. | Gate D. Activation for everyone who isn't the hold creator. |
| **6** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **7** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Off the critical path but the best ROI on the board: the fitness module, Option A.** Replacing Fitbod at $15.99/mo is ~$192/yr against a $636/yr break-even target — roughly a third of break-even recovered with no paywall, no funnel, and no Gate E dependency. **Scott's 07-26 call: A must be a FULL Fitbod replacement, not a thin MVP.** If a lifter still opens Fitbod for anything, A has not shipped.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-26

### The table-grant read — the top security item, closed

- ✅ **`anon` revoked on all 15 tables in `public`.** It had held SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES, TRIGGER and MAINTAIN on fourteen of them. **This was the Supabase default, not fourteen instances of drift** — `families` was clean only because it was the one table anyone had ever revoked (07-25).
- ✅ **RLS confirmed sound as the gate it was doing.** All 15 tables RLS-enabled. Exactly four anon-reachable policies, all RESTRICTIVE `false` denials (`profile_pins` ALL; `user_roles` INSERT/UPDATE/DELETE). No policy targets PUBLIC/ALL. Nothing permissive, nothing unconditional. **Called-out list came back empty.**
- ✅ **The asterisk that made the revoke worth doing anyway: RLS does not gate TRUNCATE, TRIGGER, REFERENCES or MAINTAIN.** Those four were held by `anon` on fourteen tables with nothing in front of them. Not reachable via PostgREST, so theoretical — but *"RLS is the sole gate"* was only ever true for half the privilege list.
- ✅ **`authenticated` reduced to exactly SELECT/INSERT/UPDATE/DELETE.** TRUNCATE/TRIGGER/REFERENCES/MAINTAIN gone everywhere. **All nine column-level grants byte-identical** — `families` still `name`-only, `profiles` unchanged. No table-level UPDATE on `families`.
- ✅ **DEFAULT PRIVILEGES FIXED FOR THE `postgres` OWNER.** New objects created by migrations no longer auto-grant `anon`. **This is the first fix aimed at the mechanism rather than the symptom** — six grant-drift instances were all the same default re-asserting itself.
- ⬜ **`supabase_admin` default-privilege residual — unreachable, logged.** `postgres` is not a member of `supabase_admin`, so that ALTER hit the exception handler. It fires only for objects the platform itself creates (extensions, platform schema work), not for migrations. **Not fixable from this connection.**
- ✅ **Verified from `pg_class.relacl`, not from the migration.** The distinction that has bitten this project before.
- ✅ **Logged-out path verified on the glass.** The signup screen renders and shows all twelve avatars per register — meaning `founder_gate_enabled()` executed with an anon session against a `system_flags` table that now grants `anon` nothing. **SECURITY DEFINER ignores table grants, confirmed live rather than on paper.**

### Activation — the five-screen first run

- ✅ **Build 1: `SetupShell.tsx` + `src/lib/setup-flow.ts`.** Ordered step registry (`SETUP_STEPS`, `setupStepIndex`, `nextSetupStepPath`), ember-lit card frame, Pip header, quiet burning-ember progress trail (no numbers), footer slot for each step's primary action, and **one centralized skip affordance that always does `navigate({to:"/board", replace:true})`.** Existing `add-family` moved in unchanged.
- ✅ **Skip-means-skip became a structural guarantee rather than a behavior.** One affordance in the shell; no future screen can get it wrong because no future screen owns it. **Better than briefed.**
- ✅ **Build 2: four screens — `first-quest`, `stock-vault`, `the-hold`, `handoff`.** **The registry claim held: adding four screens required no changes to `SetupShell.tsx` or `setup-flow.ts`.** That was the open question from Build 1 and it is answered.
- ✅ **Screens write on completion, never batched.** Verified: skipping at screen 2 left screen 1's quest in place.
- ✅ **Cold walk verified end to end** — signup → hold → hearthmates → quest → reward → the-hold → handoff → board, landing on a populated board with the user's own quest and reward on it.
- ✅ **"Open to anyone" added to screen 1** alongside named hearthmates. jAIne's assign-only call was wrong: the board has an Open Bounties strip, and forcing assignment on the first quest teaches the wrong first lesson. Also structurally necessary — `add-family` is skippable, so a hold can reach screen 1 with nobody to assign to.
- ✅ **Screen 3 ships with empty labelled frames by design.** Screenshots are manual, supplied later. A generated approximation of a real surface is worse than an honest frame.
- ✅ **Household timezone capture shipped** inside `complete_household_setup` — validated against `pg_timezone_names`, falls back to `America/Los_Angeles`, no UI, **no new grant on `families`.** The 07-15 privilege-escalation fix is intact and visible in the rewritten body. **Catalog-verified as one function, not an overload pair.**
- ✅ **`set_enabled_modules()` validates `'fitness'`.** Allowed set is exactly `household`/`fitness`. SECURITY DEFINER, pinned `search_path`, EXECUTE to `authenticated` only, PUBLIC and anon absent.
- ✅ **`The W Drapers` `enabled_modules` corrected** from `{household,training}` to `{household}`. Migration guarded by `RAISE EXCEPTION` on the row count either side — **better than briefed.** All six other households were already `{household}`.

### Recurrence — verified, and one real finding

- ✅ **Overnight daily rollover — CLOSED.** Verified on the morning board load. The failure mode was silence and it did not happen.
- ✅ **`roll_missed_dailies()` read and documented.** Filters `recurrence='daily' AND archived=false AND status<>'approved' AND due_date<household_today()`. Archives the predecessor, carries `claimed` status into the successor. **No-guilt-pile working as designed.**
- ✅ **Monthly recurrence verified sound by data.** `handle_quest_approval()` computes `date_trunc('month', today) + 1 month` — always the 1st. **The Jan-31→Feb-28 clamp-and-drift concern is structurally impossible**: the day-of-month never enters the arithmetic. Weekly is the same shape (`date_trunc('week') + 1 week`, always next Monday). All four monthly quests have been approved and each produced exactly one successor dated `2026-08-01`, `created_at` matching the parent's `completed_at` to the microsecond. No duplicates, no orphans.
- ✅ **Clean Toys discriminator — CLOSED. NOT a fourth activeness surface.** Four rows dated 07-22 through 07-25; **the first three carry `archived = true`.** The 22 and 23 were created 0.3 seconds apart — the pre-fix archive-and-respawn signature. Quest Log showed them because it applies no `due_date` filter; the member profile hid them because it applies `isActiveQuest`. **Both surfaces were correct. The LOCKED activeness decision stands as written.**
- ⬜ **NEW: unapproved weekly and monthly quests never roll forward.** `roll_missed_dailies()` filters `recurrence='daily'`; nothing else rolls anything, and weekly/monthly successors are minted *only* by `handle_quest_approval()`. So an unapproved weekly sits past-due on the board forever. **Two are stranded right now** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted). **This is a guilt pile, and it applies to two of the three cadences.** The 07-21 note that a weekly/monthly roll-forward job was "cut as redundant" was correct for *approved* quests and blind to unapproved ones.
- ⬜ **NEW: the early-approval seam.** Approving a weekly *before* its due date produces a successor dated the same week — do the chore early and it reappears immediately. Cosmetic-ish, wrong reward for being early.
- 🔵 **Recurring anchors discard the user's chosen day, by design.** A monthly created on the 15th produces a successor on the 1st. That is the 07-14 fixed-calendar-anchors decision working as written — **and it is why the recurrence chip must read `Monthly · 1st`.** The chosen date silently stops mattering after the first approval.

### Signup — rebuilt

- 🟡 **Pre-verification collection deleted.** Initial screen is email and password only. Name, avatar, role, PIN, hold name and create-vs-join all moved to the post-confirmation finish-setup form.
- 🟡 **The `emberhold.pending_setup` stash is gone** — `PENDING_KEY`, `PendingSetup`, save/load/clear, and both replay call sites. `runSetup` now takes a plain in-memory payload. **The invite-code prefill in `src/lib/invite.ts` is a separate stash and was left untouched.**
- 🟡 **Join + PIN bug fixed.** `runSetup` called `set_profile_pin` whenever `role==='parent' && pin`, but the join path always creates the profile as `kid`/`pending` server-side, so the RPC raised *"Only parents can set a PIN"* and surfaced a generic error toast at the end of signup. **That was hitting every joining adult.** No PIN field is rendered on the join path now, and `set_profile_pin` fires only when `familyChoice==='create'`.
- ✅ **Adult PIN plaintext in `localStorage` — CLOSED BY DELETION.** An open security item since the hiatus. The PIN was written raw inside the `PendingSetup` JSON blob and only cleared on a *successful* `runSetup()`, so in the different-storage-context case it persisted indefinitely. No stash, no plaintext.
- ✅ **The two-return-path fork is explained and closed.** `localStorage` is scoped per storage partition. Returning to the original tab is *definitionally* the same partition, so the replay always worked. Tapping the email link opens in the mail client's in-app webview — a different partition — so `loadPending()` returned null and the user retyped everything. **Same wall as cross-device signup, just further apart.**

### Doc + protocol

- ✅ **`master-spec.md` HEAD confirmed as the correct third version** from 07-25 late. Constitution restructure, Part I/Part II, all four folds, fitness vocabulary.
- ✅ **The `decisions.md` normalization job ran and pushed.** Zero `STATUS: proposed` lines. The three malformed 07-18 entries are fixed. The four-registers entry carries `STATUS: SUPERSEDED 2026-07-25`. *(Minor: the header's prose "Status tiers" line still omits SUPERSEDED, which is present in the format block. Cosmetic; fix on next touch.)*
- ✅ **Pip is ratified.** The Cinder vs. Holt open decision is closed — the name has been live on the auth screen for weeks and now speaks across five screens.
- ⬜ **The 07-12 guided-loop decision was drafted in chat and NEVER COMMITTED.** It is not in `decisions.md`. `status.md` compressed the whole discussion to *"four payloads, one screen,"* and jAIne built to the compression, producing a single doorway instead of the multi-screen flow Scott had specified. **Seventh instance of: state lives in the repo or it doesn't exist.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE THREE SIGNUP CHECKS — the only unverified work from 07-26.** Publish first.
  1. **Cold signup, create path, tapping the email link on a phone.** The case that has been broken from the start — Mail's in-app webview, different partition. Confirm it asks once and only once.
  2. **Cold signup, join path.** No PIN field, no error toast at the end, lands as pending.
  3. **Original-tab path.** Sign up, ignore the email, press "I confirmed," sign in. **Should now land on finish-setup identically to path 1** — this is the one that used to be seamless, and it is the proof the fork is closed rather than moved.
- 🟡 **Monday reappearance test (2026-07-27) — and it is now cheaper than it was.** Five weeklies sit at `due_date = 2026-07-27`, all created post-fix by approvals on 07-24 and 07-25. **The trigger's half is already proven by data** — correct dates, one successor per approval, no orphans. **So if nothing appears, there is exactly one candidate: the board's `isActiveQuest` predicate.** Not a build. Open the board and look.
- 🟡 **Monthly post-fix is technically unexercised.** All four monthly approvals happened 07-13 to 07-16, before the timezone fix. The only thing the fix changes is which "today" gets truncated, so the failure window is approving on the last evening of a month after UTC has rolled. Narrow. Note it, don't wait on it.
- 🟡 **STALE chip predicate — likely explained now.** The weekly wears it and the dailies don't, and the two stranded past-due unarchived weeklies are the only rows of their kind on the board. **If STALE reads `due_date < today`, that is the whole mystery.** Confirm, then fold or leave deliberately.
- 🟡 **Grant-revoke verification probe job — BRIEF DRAFTED, DEFERRED.** Grants confirmed correct on paper and the logged-out path renders, but nothing has probed the six revoked RPCs. **One distinction:** `SQLSTATE 42501` = broken grant (FAIL); any application-level error = the call reached the function body (PASS). *Sonnet · auto-accept OFF · read-only.* **Add the `families` anon revoke and the 07-26 table revoke to scope.**
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER (07-26).** Unrouted, unlinked, never loaded by a human, and nothing reads `enabled_modules`. **Scott's call: it gets finalized when fitness/Forge is built, not before.** Module intent has nothing to point at until the module exists. It stops being an open question and becomes a dependency of the fitness build — do not re-litigate it in the meantime, and do not delete it either.
- 🟡 **The ember progress trail is a visual success criterion and only Scott can judge it.** Five segments, no numbers. If it reads as a progress bar in disguise, that is information jAIne cannot have.
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, **check the wall specifically.**
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit.** Approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — new from 2026-07-26

- ⬜ **🔴 `families.timezone` CAN NEVER BE CHANGED.** It is written only on the `families` INSERT in the create branch of `complete_household_setup`. There is no UPDATE path — not for a user, not for an admin, not via any RPC. **Two consequences: the thirteen existing holds are pinned to the `America/Los_Angeles` default forever, and any hold created while someone is travelling is permanently wrong.** The 07-23 fix made the clock household-local; this makes it household-*permanent*. **A Gate B item, not a nicety.**
- ⬜ **🔴 The joiner flow has no onboarding.** See CRITICAL PATH #5. Unowned, unscoped, and it is the path every non-creator takes.
- ⬜ **Unapproved weekly/monthly quests never roll forward.** See SHIPPED above. Two rows stranded on the live board.
- ⬜ **Quest creation is ungated, and DELIBERATELY STAYS THAT WAY (decided 07-26).** No client check; the RLS INSERT policy on `quests` scopes to `family_id` and `created_by` and stops there, unlike the UPDATE/DELETE policies and `enforce_quest_update_authority`, which do check parent. **The permission is not changing.** Approval is the real gate and it holds — a kid can propose a 500-ember quest and cannot cash it. **What changes is the CURRICULUM: onboarding never teaches quest authoring to a non-approver.** Absence, not a locked door. *(Open recon question, one line: is quest creation actually discoverable from a kid's board today, or only reachable? The FAB in `BottomBar.tsx` plus entry points in `board.tsx`, `campaign.$id.tsx` and `QuickAddTray.tsx` are unconditioned, so structurally reachable — discoverability is unread.)*
- ⬜ **An established hold can land on `/onboarding/first-quest` or `/stock-vault` by URL** and be walked through the cold-start script. Nothing links there once the flow completes, **but `PipHelp` has a "Replay the intro" button** — if that ever points at this flow, an existing household gets day-one copy. **The correct behavior depends on what "replay the intro" is supposed to do, which is a design call, not a bug.**
- ⬜ **`sandbox_exec` surfaced again** in the `complete_household_setup` ACL. Still a pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable what it is. One question, not a project.**

## ⬜ OPEN — carried

- ⬜ **`enabled_modules` has no consumer.** Column and RPC exist; nothing reads them, no screen is gated, `/setup/intent` is unlinked. **Module intent was deliberately cut from the onboarding build** — fitness does not exist, and a first-run screen offering a module that opens onto nothing is worse than not asking. Either wire it when fitness ships or delete it. **Do not let it sit unowned a fourth session.**
- ✅ **`north-star.md` regenerated 2026-07-26** — constitution, module architecture, both rewritten rules, and the success definition + $636 target folded in. Closed after four sessions of asking.
- ⬜ **`master-spec.md` owes three folds from 07-26** — onboarding flow, signup posture, write-once timezone. **Read before regenerating.**
- ⬜ **The fitness module's shape is an OPEN DECISION** (Option A vs. B). **A ships first; B is not designed until A has been used for a month.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag, not a role check. Do not build now.
- ⬜ **Household timezone capture at setup — ✅ SHIPPED 07-26**, but see the write-once finding above; the item is not fully closed.
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** Logged so it is not rediscovered as an oversight.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** Currently load-bearing as a diagnostic surface; it is how both date bugs and the Clean Toys question were confirmed. **Decide deliberately.**
- ⬜ **Module navigation is unresolved.** Seven tabs is already one past mobile comfort. **Not "add another tab."**
- ⬜ **Feed verb drift — CONFIRMED LIVE 07-26.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on QUEST POSTED. Seen on the glass this session.

---

## 🟢 SECURITY TRIAGE — statuses as of 2026-07-26

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated 07-21 late, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: `confirm()` copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`. Re-confirmed 07-25 and again 07-26 from the function source.
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.** Intra-household, not cross-tenant. **Revisit if the own-session-vs-per-member-auth fork is decided.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** `wall_request_redemption`; the wall proposes and never commits. **Same revisit trigger.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.** Structurally unclearable; a definer RPC callable by authenticated users *is* the architecture. Re-confirmed twice on 07-26.

**Fixed:**
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable — FIXED 07-19.**
- ✅ **`anon` full CRUD on `families` incl. `is_founder` — FIXED 07-25.**
- ✅ **`anon` full CRUD on the other fourteen tables — FIXED 07-26.** Plus TRUNCATE/TRIGGER/REFERENCES/MAINTAIN stripped from `authenticated`, and default privileges fixed so the mechanism stops re-breaking.
- ✅ **Adult PIN plaintext in `localStorage` — CLOSED BY DELETION 07-26.** No stash, no plaintext.

**Real, open:**
- ⬜ **Activity-log forgery — actor label is client-supplied.** `append_activity` takes `_actor_label text` as a parameter. **The activity log is the receipt surface**: everything else accepted is "a kid could do a thing," while this is "a kid could make the record say an adult did it," corrupting the surface an adult would use to catch the first thing. **Fix shape:** derive from `auth.uid()`. **This is now the top open security item.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together in one audience-read pass**, *with* the own-session-vs-per-member-auth decision.
- ⬜ **`supabase_admin` default-privilege residual** — unreachable from this connection, platform-scoped. Logged as a known limit.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. Low priority.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Activity-log actor forgery** — derive actor server-side from `auth.uid()`. Top open security item.
- ⬜ **Grant-revoke verification probe job** — see PENDING VERIFY.
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Ask Lovable what `sandbox_exec` is.** Recon, not alarm.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). **Now load-bearing — the whole signup path depends on that email arriving.** ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — now long enough to deserve its own session.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users; `QA Parent`/`QA Joiner`/`QA Verify Hold`; `+ca@gmail.com`/"Testies"; the 07-21 test weeklies; the ghost "Take out the trash" successor; the 07-22/23 pre-fix dailies; **and the several throwaway holds created on 07-26 while testing onboarding (Testeron+1, The Testers, and others).** **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The joiner flow has no onboarding.** CRITICAL PATH #5.
- ⬜ **`families.timezone` has no update path.** Gate B.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted; others render the fallback placeholder until an adult manually fixes each.

## ⬜ OUTSTANDING — polish

⬜ **Feed verb drift (confirmed live 07-26)** · **Recurrence chip legibility — must read `Monthly · 1st` / `Weekly · Mon`, since the anchor discards the chosen day** · **The early-approval seam** · **Onboarding screenshots for screen 3 (manual capture + demo hold)** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** *(de-prioritized — 25 embers against 5–30 ember starter quests is two to five chores, which is the habit, not a wall)* · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The fitness module's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Re-forge reach across the 13** · **Own-session vs per-member-auth** (rewards/quests audience reads wait on it — *and two ignored scanner findings revive if it's decided*) · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED 07-22) · flat/peer holds · scripted screenshot capture (DECLINED 07-26) · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19, re-affirmed 07-21)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Two scanner findings are ignored *because of* this boundary**; deciding the own-session-vs-per-member-auth fork revives both. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **RLS AND GRANTS ARE TWO GATES, NOT ONE. (NEW 07-26.)** RLS covers SELECT/INSERT/UPDATE/DELETE. It does **not** cover TRUNCATE, TRIGGER, REFERENCES or MAINTAIN. "RLS is the sole gate" was half true for fourteen tables.
- **FIX THE MECHANISM, NOT THE INSTANCE. (NEW 07-26.)** Six grant-drift instances were one Postgres default re-asserting itself. Five reactive revokes preceded the first fix aimed at default privileges.
- **BUILD THE FRAME BEFORE THE CONTENT. (NEW 07-26.)** Splitting the onboarding work into shell-then-screens meant the shell was independently verifiable, and "did adding four screens require changing the shell?" became a one-line verdict on the previous build instead of a debugging session buried in 800 lines.
- **A REFACTOR THAT LOOKS IDENTICAL IS A SUCCESSFUL REFACTOR. (NEW 07-26.)** Build 1 was briefed as behavior-preserving; "it looks the same as before" was the pass condition, not a symptom.
- **DECOMPOSE BEFORE YOU PROMOTE. (07-25 late.)** Name a proposed structure, then list what it would be built from. **If the answer is entirely existing surfaces, it is content or theme, not architecture.**
- **Fetch the canon before producing anything. (07-25 early.)**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.** Read what landed against what was briefed, every time.
- **jAIne must not prescribe a check the frontend cannot perform. (NEW 07-26.)** The Clean Toys discriminator sat on the board for three sessions as "a 30-second glass check" reading a field that is backend-only — a fact recorded in jAIne's own principles. It resolved in one SELECT the moment it was routed correctly.
- **State a hypothesis as a hypothesis, then read before asserting. (NEW 07-26.)** jAIne made four wrong calls this session and every one was inference presented with more confidence than the evidence carried: that nothing had ever written `'training'`; that four Clean Toys rows were a guilt pile (three carried `archived = true`); that the routing fork determined whether a household saw onboarding at all (`runSetup` always routes create → the flow); and that today was Saturday.
- **An OID heuristic dates objects relative to each other, never absolutely.**
- **A code revert is not a database revert.** Undo schema forward, always.
- **The docs are not the live codebase — and can diverge silently.** Code recon syncs to origin/main first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.**
- **A Code job isn't done until the artifact is observable from outside the agent.** **Verify `origin/main`, not the agent's report.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.** *(07-26: the eleven-question onboarding recon paid for itself — it turned "extend the setup flow" into "there is no setup flow to extend" before a single credit was spent on the wrong assumption.)*
- **Sweep the class before fixing the instance.** Seven functions (07-21); six date sites (07-23); one table read led to fourteen (07-25 → 07-26).
- **A defensive layer is not verified by the migration that adds it.** **A catalog read is not a glass test, and HTTP 200 is not "renders."** *(07-26: `/board` returning 200 proved nothing about a revoke that could only have broken the logged-out path.)*
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (the tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering. (NEW 07-26.)** Twice a bundled write ran before the read-only tasks it was sequenced behind. Harmless both times because the write was inert. **Put the write in its own prompt whenever the reads are what determine whether the write is safe.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-25 (late)** — the constitution restructure. `master-spec.md` fully regenerated with all four overdue folds. Registers reverted to aesthetic-only; fitness the sole module. Two constitutional rules rewritten. Endure read and scoped out on a platform wall.
- **2026-07-25 (early)** — the module reframe. `families.enabled_modules` + `set_enabled_modules()` + `/setup/intent` + `src/lib/progression.ts` (10/10 tests, zero Supabase imports — the only survivor of the teardown). An eight-table parallel tenancy built and dropped the same night.
- **2026-07-23** — the household-local date seam. `families.timezone` + `household_today()`; six `CURRENT_DATE` violations across two functions.
- **2026-07-21 (late)** — the SECURITY DEFINER grant surface. Seven functions carrying `PUBLIC` + `anon`; one grant-then-revoke migration.
- **2026-07-21 (evening)** — recurring-quest visibility. `isActiveQuest` unified into `quest-helpers.ts`; successor anchoring fixed; the timezone skew found.
- **2026-07-19 (audit)** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED. Isolation model verified sound. Two grant-drift breaches closed.
- **2026-07-19 / 07-18** — wall display-mode fine-tune on physical glass. Avatar transport end-to-end: 48-roster committed, tier-tagged 16/32, global gate as a DB value.
- **2026-07-16** — roster "no members" root-caused to zero Data-API grants for `authenticated` across 14 tables. Pending→admission routing fixed (`28ab40d`).
- **2026-07-15** — admit-on-approval shipped. Live privilege-escalation in `complete_household_setup` fixed. `recurrence_day` prod crash fixed. Pending-member roster leak fixed.
- **2026-07-14** — admit-on-approval data layer. Hub open-bounty count unified. Recurrence reworked to fixed calendar anchors.
- **2026-07-12** — Claude Code proven as a build lane. Auth hardening. Hold Alpha built. 13 real accounts confirmed.
- **2026-07-11** — engine daily respawn verified. Avatar roster regenerated (48 characters, 19 sheets). Lovable ↔ GitHub sync connected.
- **2026-07-10** — engine auto-approval, daily board fix, edit-form parity. XP killed. Vault kid/adult/couples-rail. Quest audience filter. Docs migrated; `north-star.md` created.
- **2026-07-03 → 06-26** — Avatar Overhaul designed (Feast→Hall). Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, monthly recurrence + audience, Campaigns, Calendar, the Vault, PIN, Quest Log.

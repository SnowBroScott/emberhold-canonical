# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-23** — *the household-local date seam: the daily-quest bug root-caused to a different function than anyone expected, then fixed as a class rather than an instance.* Started as "dailies have the same problem weeklies had" and ended by generalizing the 07-21 same-clock rule. **The create path was innocent** — `create.tsx` does not branch on recurrence and stamps `due_date: todayIsoDate()` for all four types. jAIne's confident opening hypothesis (a daily branch overriding the stamp at insert) was **wrong**; the recon brief was written to disprove it rather than confirm it, which is the only reason it was caught in one pass. **The actual mechanism:** `roll_missed_dailies()` fires unconditionally on every `/board` mount, and `create.tsx` navigates to `/board` on success — so the RPC ran seconds after creation, judged the fresh row against server UTC (`due_date < CURRENT_DATE`), declared it "missed," archived it, and respawned it dated tomorrow. Correct for one round-trip, then clobbered. **The enumeration is what made the session worth it:** a full grep of `supabase/` for household-local date math found **six live violations across two functions**, not one — `roll_missed_dailies()` (filter + respawn) and `handle_quest_approval()` (daily, weekly, monthly, and fallback successor anchors, all four). **The fix shape changed mid-session because `handle_quest_approval` is a trigger** — no caller, no place to pass a client-supplied date. That structurally killed the client-stamp approach and forced the right answer: `families.timezone` + a `household_today(fam)` helper, both functions deriving from it. **One migration, zero frontend changes, and the client-supplied-date trust-widening evaporated with it.** Catalog re-read confirms no `CURRENT_DATE` remains in any expression touching `due_date`. **Create-path leg verified on the glass. Two legs still unverified.** One new possible defect surfaced during verification and is unresolved — see OPEN.

Last session (prior): **2026-07-21 (late)** — *the SECURITY DEFINER grant surface: enumerated, root-caused, fixed, and re-verified clean. The P4×L8 function-grant asterisk is closed.* Started as "which of these scanner findings can I ignore" and ended by killing the mechanism that produced two of the three grant-drift breaches this project has had. **The 10-item Lovable scan triaged end to end:** five ignored (validated — all five correct), three real-and-queued, one real-and-new, one fixed tonight. **Root cause found and it is structural, not incidental:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function, so every SECURITY DEFINER function Emberhold has ever shipped arrived anon-callable unless someone remembered to revoke. Seven functions were carrying it — and the set was **chronological, not random**. **Not exploitable** (every affected RPC guards on `auth.uid()` internally) — but a defense-in-depth layer was missing and was being removed automatically on every new function. **Fixed as one grant-then-revoke migration.** **Re-read verified: zero `PUBLIC`/`anon` grants on any SECURITY DEFINER function in `public`.**

Last session (prior): **2026-07-21 (evening)** — *recurring-quest visibility, root-caused across four layers and fixed.* **Four distinct defects, only the last of which was the one Scott walked in with:** (1) three surfaces each defined "active quest" independently — unified behind a single imported `isActiveQuest` predicate; (2) the recurring successor's `due_date` anchored to the *completed instance's* old date — re-anchored to approval date via migration; (3) a pre-migration ghost successor still carries a stale future date; (4) **the actual opening bug — a timezone skew** at the create path, fixed and verified on the glass. **Two durable models captured to decisions.md** — the recurring lifecycle, and the same-clock date rule. *(2026-07-23: the same-clock rule has been AMENDED — see decisions.md. Its NOTE predicting the `handle_quest_approval` seam was correct and that seam is now closed.)*

Last session (prior): **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit was RUN — the mortal-peril item, breached where it was weak, fixed, and re-verified closed on the live glass.* Two throwaway households, real confirmed owner JWTs, every probe fired live against the deployed DB. **The isolation model is verified sound.** Two breaches found — both grant drift — and both closed in one migration, then re-verified. **The security distribution gate cleared**, with a grant-enumeration asterisk — *now closed for functions (07-21 late); still open for tables.*

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate, a tenant-isolation model verified sound under live authenticated attack, a SECURITY DEFINER grant surface that is provably clean with a convention behind it — and, as of 07-23, a quest date model that derives household-local time from a single named server-side function instead of six scattered `CURRENT_DATE` calls.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**The date seam is closed in code and verified in the catalog. It is verified on the glass for creation only.**

> **Two legs of the date fix remain unverified, and they are the ones that can't be faked.** The rollover (does a daily actually roll overnight now, or does it silently never roll?) and Monday's reappearance test. Neither is testable on demand. See PENDING VERIFY.

> **A possible FOURTH activeness surface surfaced during verification and is unresolved.** A daily quest in progress and assigned to a member did not appear in that member's roster/profile "active quests" list, while showing correctly in the Quest Log. If that quest's `due_date` is future-dated it is pre-fix debris and nothing is wrong. If it is today-or-earlier, then the member profile view carries its own activeness predicate that the 07-21 unification never swept — which would mean a LOCKED decision claiming three surfaces were unified is partially untrue. **One-question discriminator, first thing next session.** See OPEN.

**The remaining structural gap is activation, not security.**

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. This is the top structural item before strangers.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **✅ P4×L8 tenant-isolation audit — SECURITY GATE CLEARED (2026-07-19).** Live authenticated two-household attack; boundary verified sound; two grant-drift breaches found, fixed, re-verified CLOSED. **Function-grant residual CLOSED 07-21 late.** **Remaining residual (non-blocking): the TABLE-grant read.** | — (was: Distribution / Gate B) |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix. **The top structural blocker.** | Gate D. Activation. |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen already received it. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready; gate mechanics built and secured. | — |

**Next-action fork (Scott's call):** the **Clean Toys discriminator** (30 seconds, and it decides whether a LOCKED decision is accurate — do this one first regardless), the **master-spec fold** (now three items deep and the oldest debt on the board), the **table-grant read** (cheap, closes the last security asterisk entirely), the **auth email** (contained, first-impression), or **Pip onboarding** (the real activation gap).

---

## 🟢 SHIPPED / RESOLVED — 2026-07-23: the household-local date seam

**The symptom.** A newly created quest with `recurrence = daily` did not appear on the board. It appeared in the Quest Log (which applies no `due_date` filter) carrying a `due_date` of tomorrow. Same presentation as the 07-21 bug; different cause entirely.

**The wrong hypothesis, named because the process caught it.** jAIne's confident opening call was that the create path branched on recurrence and overrode the 07-21 `todayIsoDate()` stamp. It does not — `create.tsx` builds one flat payload with no recurrence branch, and line 153 stamps `due_date: todayIsoDate()` for all four types unconditionally. The recon brief was written to force a ruling on **(a) receives-then-overwritten vs (b) never-receives-it**, rather than to confirm the hypothesis. That framing is why one recon pass found the real mechanism instead of validating a wrong one.

**The real mechanism.** `board.tsx` fires `supabase.rpc("roll_missed_dailies")` unconditionally on every mount, and `create.tsx` navigates to `/board` on successful insert. The RPC therefore ran **seconds after creation**. Its staleness filter (`due_date < CURRENT_DATE`) and its respawn value both evaluated in the **server's** timezone. For a household west of UTC creating in the evening, the fresh, correctly-stamped row was judged stale, archived, and respawned dated tomorrow — where `isActiveQuest` correctly hid it. Unique to daily because the RPC is scoped `recurrence = 'daily'` only.

**The enumeration — the part that made the session worth more than the bug.** Rather than fixing the one instance, a full grep of `supabase/` for household-local date math ran first. It found **six live violations across two functions**:

- `roll_missed_dailies()` (LIVE: migration `20260715045118`) — the staleness filter and the respawn value. **2 sites.**
- `handle_quest_approval()` (LIVE: migration `20260721050411`) — the daily successor, the weekly `date_trunc` anchor, the monthly `date_trunc` anchor, and the fallback branch. **4 sites.** *This confirms the NOTE parked in the 07-21 same-clock decision: the seam did exist there.*
- Frontend: **zero violations.** `todayIsoDate()` is used uniformly on the write path.

**The fix shape changed mid-session, and the reason is structural.** The obvious fix — pass a client-supplied `_today` — works for `roll_missed_dailies` (it's an RPC with exactly one call site) and is **impossible** for `handle_quest_approval`, which is a BEFORE-UPDATE trigger with no caller. jAIne had explicitly rejected a stored household timezone the turn before, on reasoning that was backwards: the "parent travels and it goes stale" objection is precisely the case where a *client-supplied* date breaks, since an approval fired from another timezone would re-anchor the household's recurrence to the traveling device's clock. **The household has a timezone. The device is a proxy for it that works only because everyone is in the same one.**

**What shipped.** One migration, Lovable lane:
- `families.timezone` — IANA name, text, NOT NULL, `DEFAULT 'America/Los_Angeles'`. All 13 existing households backfill correctly.
- `household_today(fam uuid) RETURNS date`, STABLE — the single canonical way any server-side code asks what day it is for a household.
- All six sites replaced. Both functions keep their existing signatures. **`board.tsx:40` unchanged. Frontend lane: zero changes.**

**Deliberately out of scope, and it is a decision not an oversight:** the `DEFAULT CURRENT_DATE` on `quests.due_date` was left in place. It only fires on inserts that omit the column, which no app path does. See `decisions.md`.

**Two things the fix shape bought beyond correctness.** The client-supplied-date trust-widening (a device-kid passing tomorrow's date to churn a daily early) never came into existence. And the correct thing to type is now a named function that means what it says — the next person reaching for `CURRENT_DATE` near a date column has to actively ignore an obvious alternative.

**Verified.** Catalog re-read of both live function bodies: no `CURRENT_DATE` in any expression touching `due_date`; all anchors derive from `household_today(NEW.family_id)` / `household_today(fam)`. **Glass: a daily created post-migration appears on the board and in the member roster immediately.**

---

## 🟡 PENDING VERIFY

- 🟡 **Daily rollover across midnight (NEW 07-23) — THE LEG THAT MATTERS.** `roll_missed_dailies` is the archive-and-respawn path and **both** its filter and its write changed. The failure mode is not a wrong date — it is dailies **silently never rolling at all**: yesterday's instance sitting stale, no successor appearing. The create-path test does not touch this. **First board load of the morning: confirm yesterday's daily is gone and a fresh one is open and dated today.**
- 🟡 **Monday reappearance test — now carrying two payloads (STAGED, 2026-07-27).** Originally staged to verify the 07-21 anchor migration. `handle_quest_approval` has been rewritten since, so Monday now verifies the anchor logic **and** `household_today()` in trigger context simultaneously. **If it fails there are two candidates, not one.** *Nothing to do until Monday — just look.*
- 🟡 **Fourth-activeness-surface discriminator (NEW 07-23) — 30 seconds, do it first.** See OPEN below.
- 🟡 **Grant-revoke verification probe job (07-21 late) — BRIEF DRAFTED, DEFERRED BY SCOTT.** The grants are confirmed correct *on paper*; nothing has confirmed they didn't break a live path. **The whole job is one distinction:** `SQLSTATE 42501, permission denied for function` = the grant is broken (FAIL). Any application-level error = the call reached the function body, so the grant works (PASS). Probe all six revoked RPCs plus three untouched controls (`current_family_id`, `member_spendable`, `approve_redemption`), using a real authenticated JWT, non-existent UUIDs, no writes. Separately confirm `enforce_quest_family_refs` IS denied to authenticated and that the trigger is still attached. **Sonnet · auto-accept OFF · read-only.**
- 🟡 **`handle_quest_approval` anchor migration (07-21) — landed but not independently confirmed on `origin/main`.** Monday's test is its real verification; if Monday fails, confirm the migration landed **before** re-diagnosing.
- 🟡 **Avatar render fallback ("the floor")** — glass test: pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, check the **wall** specifically.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Briefly flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit.** Glass test: approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — from the 07-23 session

- ⬜ **🔴 POSSIBLE FOURTH ACTIVENESS SURFACE — DISCRIMINATE FIRST, IT'S ONE QUESTION.** During verification, "Clean Toys Downstairs" (daily, IN PROGRESS, assigned to Mia) appeared in the Quest Log but **not** in Mia's roster/profile "active quests" list, which read `1`. **Expand it in the Quest Log and read its due date.**
  - **Future-dated → pre-fix debris.** The migration fixed the code, not existing rows. Nothing is wrong. Delete it and move on.
  - **Today or earlier → a fourth surface carrying its own predicate.** The 07-21 unification swept `board.tsx`, `Briefing.tsx` and `wall.tsx`. The member profile/roster view was **not** in that list. That would make the LOCKED activeness decision partially untrue as written, and needs a Code recon to find every remaining local predicate.
  - **Why it outranks its size:** the harm is a kid claiming a quest and not seeing it on her own profile. That is the "where'd my chore go" failure, aimed at the person the app exists to be fun for.
- ⬜ **STALE chip may be a fourth read of `due_date`.** In the Quest Log the weekly wears a **STALE** badge and the dailies do not. If that badge derives from `due_date` independently, it should read from the shared predicate rather than computing its own. Verify, then fold or leave deliberately.
- ⬜ **Household timezone capture at setup — a Gate B item.** `families.timezone` defaults to `America/Los_Angeles`, which is correct for all 13 current households and **silently wrong for the first stranger in Ohio**, whose dailies would roll at 9pm local. Fix is small and frontend-only: capture `Intl.DateTimeFormat().resolvedOptions().timeZone` during household setup. **Belongs with Pip onboarding, not bolted onto the date migration.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** Logged as a decision so it is not rediscovered as an oversight. Only fires on inserts that omit the column; no app path does. Revisit if a non-app insert path is ever added.

---

## 🟢 SECURITY TRIAGE — Lovable scanner findings — statuses as of 2026-07-21 late

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated 07-21 late, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: `confirm()` copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`.
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.** The shared-session walk-up boundary. Intra-household, not cross-tenant. **Revisit if the own-session-vs-per-member-auth fork is decided.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** `wall_request_redemption`; the wall proposes and never commits. **Same revisit trigger as above.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.** Structurally unclearable. See `decisions.md`.

**Fixed:**
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable (#7) — FIXED 07-19, independently re-confirmed 07-21.**
- ✅ **Broad SECURITY DEFINER surface (#8) — CLOSED.**

**Real, open:**
- ⬜ **Activity-log forgery — actor label is client-supplied (07-21 late). REAL. NOT accepted-by-association.** `append_activity` takes `_actor_label text` as a parameter. Intra-household and not a distribution blocker, **but the activity log is the receipt surface**: everything else in the accepted pile is "a kid could do a thing," while this is "a kid could make the record say an adult did a thing," which corrupts the surface an adult would use to catch the first thing. **Fix shape:** derive the actor server-side from `auth.uid()`. Small, contained, not urgent.
- ⬜ **Adult PIN plaintext in `localStorage` during signup (#5) — CONFIRMED, QUEUED. CREDIT-FREE.** Fix: collect it in post-confirmation finish-setup, don't persist client-side. Frontend-only → Code commit.
- ⬜ **Kids read `adults_only` reward names/costs (#6)** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together, in one audience-read pass.** Both tangle with the parked own-session-vs-per-member-auth decision. Fix them *with* that decision, not before, and not separately from each other.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit` to reproduce the scanner's "1 vuln." Low priority.

---

## ⬜ OPEN — carried from the 07-21 (evening) recurring-quest session

- ⬜ **`master-spec.md` fold — NOW THREE ITEMS DEEP AND THE OLDEST DEBT ON THE BOARD.** Owes: the recurring lifecycle, the activeness model (both 07-21), and the household-local date rule (07-23). All three are in `decisions.md`. **Its own short session** — read the current spec, fold, replace. Deliberately not regenerated blind at session close, twice now.
- ✅ **~~The same timezone seam likely exists in `handle_quest_approval`.~~ CLOSED 07-23.** It did. Four sites. Fixed with the rest.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** **Decide deliberately** rather than letting it stay accidental. *(07-23: this is currently load-bearing as a diagnostic surface — it's how both date bugs were confirmed. That's an argument for keeping it, but make it on purpose.)*

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (late): the SECURITY DEFINER grant surface

**✅ Row 0029 (signed-in users can execute SECURITY DEFINER) — PERMANENTLY IGNORED.** Structurally unclearable. A definer RPC callable by authenticated users *is* the architecture. Logged in `decisions.md` so it is never re-litigated.

**✅ Row 0028 (public/anon can execute) — REAL, ROOT-CAUSED, FIXED.** **Seven functions carried `PUBLIC` + `anon`.** **Root cause:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function. The affected set was chronological, not random — `enforce_quest_family_refs` was created **inside the 07-19 grant-drift fix bundle** itself. **Every revoke this project has ever done was a reactive one-off against a mechanism that re-breaks the default automatically.** *Exploitability: NO* — all six real RPCs guard on `auth.uid()` internally. **The fix:** one migration, **grant-then-revoke in that order** (`authenticated` inherits from `PUBLIC`). **Re-verified** by full catalog re-read.

**✅ The 07-19 founder revoke held.** First confirmation from outside the audit's own re-verify.

**✅ The ignored-issues list validated — five for five.** **Caveat the dashboard cannot hold:** those five are ignored for *three different reasons*, and two of them are accepted **only because the shared-session model is what it is.** If the own-session-vs-per-member-auth fork is ever decided, those two become live work.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (evening): recurring-quest visibility, four layers deep

**Layer 1 — three definitions of "active," none shared. ✅ FIXED.** One predicate, `isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts`, implementing `!archived && status !== 'approved' && (due_date == null || due_date <= today)`, imported by `board.tsx`, `Briefing.tsx`, `wall.tsx` (`42fcddb`). **Both mechanisms are required:** `status` hides completed-this-cycle; `due_date` hides not-yet-this-cycle. *(07-23: possibly not a complete sweep — see OPEN.)*

**Layer 2 — the successor anchored to the wrong date. ✅ FIXED (migration).** Re-anchored to approval date for weekly and monthly.

**Layer 3 — pre-migration ghost. ⬜ COSMETIC, OPEN.**

**Layer 4 — THE ACTUAL BUG: a timezone skew. ✅ FIXED, VERIFIED ON GLASS.** The create path now stamps `due_date: todayIsoDate()` explicitly.

**One phantom killed.** The "favorite-chip silently auto-assigns the assignee" bug **does not exist**. Logged DECLINED.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Verdict-level record only — attack/exploit specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound (SECURE, denials shown live):** cross-tenant reads and writes; `current_family_id()` integrity; every household-scoped SECURITY DEFINER RPC; the `family_xp` view; the insert trigger; `system_flags` write.

**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` family-scoped; new `enforce_quest_family_refs` trigger. **Re-verified.**

**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` revoked from `authenticated`. **Re-verified 07-19, independently re-confirmed 07-21.**

**Teardown (a) done.** **Residual → Scott's dashboard cascade:** the two throwaway families (Alpha/Bravo) + their 3 auth users.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning)

**Wall display-mode fine-tune — verified on the physical glass.** Avatars enlarged (~18% of tile width). Quest-count line wrap killed. Two durable wall-rendering rules captured.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-18 (evening)

**Avatar transport — shipped end-to-end.** 48-roster into `src/assets/avatars/`, committed, synced. Tier-tagged free/founder (16/32). Global gate as a DB value (`system_flags.founder_gate_enabled`, seeded false) — **flip is a one-line UPDATE, not a build.** Re-forge: 28/28 invalidated, run live.

**QA burn-down — three shipped, verified.** **QA #5 reclassified** as cross-hold super-admin / tier-2 support → distribution-era.

---

## ⬜ OPEN FINDINGS — from the avatar session

- ⬜ **Re-forge reach across the 13 households (real defect, later fix).** Re-forge only prompts the member who logs in; others render the fallback placeholder until an adult manually fixes each.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." Verify and align.

---

## 🟢 SHIPPED — 2026-07-18 (day)

**Doc-protocol repair + catch-up committed.** Six 07-17 decisions committed; sync-to-origin rule folded into `session-protocol`.

**SnowDad Vault spendable = 0 — cleaned (data, not code).** **Two findings logged:** `parent_self_redeem` doesn't write `activity_log` (NOTED); `member_spendable` silently clamps overdraw to 0 (NOTED — the clamp-hiding facet remains open).

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **TABLE-grant read — the last open P4×L8 slice.** Function grants are provably clean as of 07-21 late; **tables have not been re-read since the 07-16 outage**, where fourteen tables lost their `authenticated` Data-API grants. Same disease, different organ. Cheap catalog query; closes the asterisk entirely. ⚠️ *Findings detail stays in the Code/Workstream session.*
- ⬜ **Grant-revoke verification probe job** — see PENDING VERIFY. Brief drafted 07-21, deferred.
- ⬜ **Activity-log actor forgery** — derive actor server-side from `auth.uid()`.
- ⬜ **`sandbox_exec` — unexplained role holding EXECUTE on every function in `public` (07-21 late).** Pre-existing, so **not drift** — almost certainly a Lovable platform role. But it is a broad-execute role neither Scott nor jAIne can account for. **Ask Lovable what it is and what it's for.** Recon, not alarm.
- ⬜ **PIN-plaintext-in-localStorage (#5)** — credit-free Code frontend commit.
- ⬜ **Rewards + quests audience reads (#6 and its quest twin)** — deferred, fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Household timezone capture at setup** — see OPEN 07-23. Gate B item; belongs with Pip onboarding.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users — Scott's dashboard cascade. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies", the 07-21 test weeklies, the ghost "Take out the trash" successor, and **the 07-22/23 pre-fix test dailies (NEW) — these carry future `due_date` values and will keep presenting as a failed fix every time this area is tested.** **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. **The top structural blocker.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint** · **"Forgot PIN" confirm() copy** ("only the account owner" is inaccurate) · **STALE chip predicate** (see OPEN 07-23).

---

## 🅿️ PARKED

See `parking-lot.md`. **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Re-forge reach across the 13** · **Own-session vs per-member-auth** (rewards/quests audience reads wait on it — *and two ignored scanner findings revive if it's decided*) · Favorites on the wall · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19, re-affirmed 07-21)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Two scanner findings are ignored *because of* this boundary** (PIN-lock bypass, on-behalf redemption); deciding the own-session-vs-per-member-auth fork revives both. The fork is parked. See `decisions.md`.

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.** Live-schema-drift is a 5×-observed pattern.
- **A Code job isn't done until the artifact is observable from outside the agent.** **Verify `origin/main`, not the agent's report.**
- **Recon before build, every time.** Reasoning from screenshots instead of reading the source cost three wrong diagnoses on 07-21.
- **Brief recon to DISPROVE, not to confirm.** *(New 07-23.)* jAIne's opening hypothesis was confidently wrong; the brief forced a ruling between two named alternatives rather than asking for validation, and the real mechanism came back in one pass. A brief that asks "confirm X" gets X.
- **Sweep the class before fixing the instance.** *(New 07-23.)* Two sessions running, the enumeration found more than the symptom: seven functions carrying a grant default (07-21), six date sites across two functions (07-23). The read is cheap; the second Lovable credit is not.
- **Enumerate before you triage.** *(07-21 late.)* A scanner that reports a *category* without naming instances cannot be triaged from the dashboard.
- **A defensive layer is not verified by the migration that adds it.** *(07-21 late.)* Fix → re-read the catalog → confirm on the live artifact. **And a catalog read is not a glass test** *(07-23)* — it proves the code is right, not that the behavior is.
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member. *(Re-confirmed 07-19 twice.)*

---

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster "no members" root-caused to zero Data-API grants for `authenticated` (a live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). *(Also proved Code can browser-provision confirmed test households end-to-end.)* **⚠️ This is the reason the table-grant read is still owed — it has not been re-read since.**

## 🟢 SHIPPED — 2026-07-15

Live privilege-escalation in `complete_household_setup` fixed. Admit/deny enum crash fixed. `recurrence_day` prod crash fixed (`172a07f`). Pending-member roster leak fixed (`b31c92c`).

## ✅ SHIPPED — 2026-07-14

Admit-on-approval data layer. Hub open-bounty count unified. Recurrence reworked to fixed calendar anchors.

## ✅ SHIPPED — 2026-07-12

Claude Code proven as a build lane. Auth hardening (email confirmation, holding-state signup, password reset, min length 6→8). Hold Alpha built (the P4×L8 instrument). 13 real accounts confirmed.

## ✅ SHIPPED — 2026-07-11

Engine daily respawn verified. Vocabulary cleanup. Safe-area inset fix. Avatar roster regenerated (48 characters, 19 sheets). Lovable ↔ GitHub sync connected.

## ✅ SHIPPED — 2026-07-10

Engine auto-approval + daily board fix + edit-form parity. XP killed. Vault kid/adult/couples-rail. Quest audience filter. Over-broad anon EXECUTE grant revoked. Docs migrated; `north-star.md` created.

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Rename confirmed still present on the live member editor 2026-07-18 — queued for the Haiku sweep.*
- **2026-06-29 → 06-26** — Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, monthly recurrence + audience, Campaigns, Calendar, Briefing, the Vault, PIN, Quest Log.

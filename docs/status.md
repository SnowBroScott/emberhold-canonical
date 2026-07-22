# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-21 (late)** — *the SECURITY DEFINER grant surface: enumerated, root-caused, fixed, and re-verified clean. The P4×L8 function-grant asterisk is closed.* Started as "which of these scanner findings can I ignore" and ended by killing the mechanism that produced two of the three grant-drift breaches this project has had. **The 10-item Lovable scan triaged end to end:** five ignored (validated — all five correct), three real-and-queued, one real-and-new, one fixed tonight. **Root cause found and it is structural, not incidental:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function, so every SECURITY DEFINER function Emberhold has ever shipped arrived anon-callable unless someone remembered to revoke. Seven functions were carrying it — and the set was **chronological, not random**: the admit-on-approval family (07-14/15), `wall_request_redemption` (the wall), and `enforce_quest_family_refs` — *which shipped inside the 07-19 grant-drift fix bundle itself.* **Not exploitable** (every affected RPC guards on `auth.uid()` internally; the 07-19 audit fired live anon probes and got denials) — but a defense-in-depth layer was missing and was being removed automatically on every new function. **Fixed as one grant-then-revoke migration** (order mattered: `authenticated` inherits from `PUBLIC`, so revoking first would have taken out signup and admit). **Re-read verified: zero `PUBLIC`/`anon` grants on any SECURITY DEFINER function in `public`.** The convention that prevents recurrence is logged LOCKED-proposed in `decisions.md`.

Last session (prior): **2026-07-21 (evening)** — *recurring-quest visibility, root-caused across four layers and fixed.* Started as "weekly quests aren't showing on the board" and ended three layers deeper than the opening symptom. **Four distinct defects, only the last of which was the one Scott walked in with:** (1) three surfaces each defined "active quest" independently and disagreed — unified behind a single imported `isActiveQuest` predicate; (2) the recurring successor's `due_date` anchored to the *completed instance's* old date, so a late-completed weekly reappeared immediately instead of resting — re-anchored to approval date via migration; (3) a pre-migration ghost successor still carries a stale future date (harmless, hidden from board, visible in Quest Log); (4) **the actual opening bug — a timezone skew.** `due_date` was stamped by the server in UTC and judged by the client in Pacific, so any quest created in the evening landed dated "tomorrow" and was hidden by its own visibility filter. Fixed at the create path; **verified on the glass in the failure window** (created → showed → completed → hid). One leg deliberately left open: the Monday reappearance test, staged and unfakeable. **Two durable models captured to decisions.md** — the recurring lifecycle, and the same-clock date rule.

Last session (prior): **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit was RUN — the mortal-peril item, breached where it was weak, fixed, and re-verified closed on the live glass.* Two throwaway households, real confirmed owner JWTs, every probe fired live against the deployed DB. **The isolation model is verified sound.** Two breaches found — both grant drift — and both closed in one migration, then re-verified. Finding A: `member_spendable()` wasn't family-scoped + quest FK columns accepted foreign-family profiles. Finding B: `families.is_founder` was owner-writable → self-serve paywall bypass. **The security distribution gate cleared**, with a grant-enumeration asterisk — *now closed for functions (07-21 late); still open for tables.*

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate, a tenant-isolation model verified sound under live authenticated attack, a coherent recurring-quest lifecycle — and, as of 07-21 late, a SECURITY DEFINER grant surface that is provably clean and has a convention behind it so it stays that way.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**The security gate is cleared and the last function-grant asterisk is closed.**

> **Function grants: provably clean.** Every SECURITY DEFINER function in `public` enumerated; zero `PUBLIC`/`anon` EXECUTE. **Table grants: NOT yet re-read.** The 07-16 roster outage was fourteen tables losing their `authenticated` Data-API grants — same disease, different organ, and tonight's read does not touch it. The honest claim is *"SECURITY DEFINER function grants are provably clean,"* not *"the grant surface is provably clean."* One cheap read closes the difference.

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

**Next-action fork (Scott's call):** the **grant-verification probe job** (drafted, deferred — closes tonight's work honestly), the **table-grant read** (cheap, closes the last security asterisk entirely), the **auth email** (contained, first-impression), or **Pip onboarding** (the real activation gap). Plus, still overdue and still cheap: the **master-spec fold** for the recurring lifecycle.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (late): the SECURITY DEFINER grant surface

**The scan that started it.** Lovable's basic scan surfaced **10 findings** (up from the 8 triaged on 07-19 — two new, both wall-shaped). Two rows were bare Supabase database-linter lints (0028 anon-executable, 0029 authenticated-executable) with **no function names attached** — a category flag, not a finding. That's why they couldn't be triaged from the dashboard, and it's what forced the enumeration read.

**✅ Row 0029 (signed-in users can execute SECURITY DEFINER) — PERMANENTLY IGNORED.** Structurally unclearable. A definer RPC callable by authenticated users *is* the architecture — `admit_pending_member`, `wall_request_redemption`, `member_spendable` are all meant to be reachable by a signed-in user. This lint will fire forever regardless of what's fixed. Logged in `decisions.md` so it is never re-litigated on a future scan.

**✅ Row 0028 (public/anon can execute) — REAL, ROOT-CAUSED, FIXED.**

*The read.* Full enumeration of SECURITY DEFINER functions in `public` with their EXECUTE grantees. **Seven carried `PUBLIC` + `anon`:** `admit_pending_member`, `complete_household_setup`, `deny_pending_member`, `get_pending_membership`, `list_pending_members`, `wall_request_redemption`, and `enforce_quest_family_refs`.

*The root cause — and it is the important part.* **Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function.** The affected set was chronological, not random: everything older had been cleaned by some past reactive revoke; everything created since inherited the default. `enforce_quest_family_refs` was created **inside the 07-19 grant-drift fix bundle** — the migration that closed two grant-drift breaches shipped a third one in the same file. **Every revoke this project has ever done was a reactive one-off against a mechanism that re-breaks the default automatically.** Three-for-three on grant drift; this is why.

*Exploitability: NO.* All six real RPCs guard on `auth.uid()` internally and reject anonymous callers; the seventh is a trigger function whose grant is moot in trigger context. The 07-19 audit fired live anon probes at the definer surface and got denials. **This was a missing defense-in-depth layer, not an open door** — but it meant the next RPC written without an internal `auth.uid()` check would have been anon-callable with nothing in the process to catch it.

*The fix.* One migration, **grant-then-revoke in that order** — `authenticated` inherits `EXECUTE` from `PUBLIC`, so revoking first would have silently killed signup, household setup, join-by-code and admit. Six RPCs granted explicitly to `authenticated`, then revoked from `PUBLIC` and `anon`. `enforce_quest_family_refs` stripped of all API-role grants to match the other three `enforce_*` triggers. `create_family` and `find_family_by_code` deliberately untouched (intentionally unexposed).

*Re-verified.* Full re-read of the same catalog query: **zero `PUBLIC`/`anon` EXECUTE on any SECURITY DEFINER function in `public`.** Six RPCs `authenticated`-only. `enforce_quest_family_refs` now matches the other triggers exactly. Controls untouched. Independent confirmation, not a self-report.

**✅ The 07-19 founder revoke held.** `founder_gate_enabled` and `my_household_is_founder` came back `authenticated`-only in the enumeration — first confirmation of that fix from outside the audit's own re-verify.

**✅ The ignored-issues list validated — five for five, no corrections.** "Forgot PIN" takeover (false positive) · join-code → Parent admin (false positive) · adult PIN lock bypass (known-accepted walk-up boundary) · redemption submitted on behalf of another member (**by design — the wall proposes, never commits**) · 0029 (structurally unclearable). **Caveat carried into the docs, because the dashboard cannot hold it:** those five are ignored for *three different reasons*, and two of them (PIN-lock bypass, on-behalf redemption) are accepted **only because the shared-session model is what it is.** If the own-session-vs-per-member-auth fork is ever decided, those two stop being by-design and become live work — and nothing in that panel will ever say so.

---

## 🟡 PENDING VERIFY

- 🟡 **Grant-revoke verification probe job (NEW 07-21 late) — BRIEF DRAFTED, DEFERRED BY SCOTT.** The grants are confirmed correct *on paper*; nothing has confirmed they didn't break a live path. **The whole job is one distinction:** a revoked grant fails exactly one way — `SQLSTATE 42501, permission denied for function`. **42501 = the grant is broken (FAIL). Any application-level error (invalid uuid / not found / not authorized / business-rule rejection) = the call reached the function body, so the grant works (PASS).** The probes do not need to succeed; they need to get past the permission check. Probe all six revoked RPCs plus three untouched controls (`current_family_id`, `member_spendable`, `approve_redemption` — a 42501 on a control means the migration overreached), using a real authenticated JWT, non-existent UUIDs, and no writes. Separately confirm `enforce_quest_family_refs` IS denied to authenticated (correct for that one) and that the trigger is still attached by reading its definition. **Sonnet · auto-accept OFF · read-only.**
- 🟡 **Monday reappearance test — the recurring lifecycle's one unfakeable leg (STAGED).** On **Monday 2026-07-27**, open the board and confirm the completed weekly reappears open and actionable. *Nothing to do until Monday — just look.*
- 🟡 **`handle_quest_approval` anchor migration — landed but not independently confirmed on `origin/main`.** Monday's test is its real verification; if Monday fails, confirm the migration landed **before** re-diagnosing.
- 🟡 **Avatar render fallback ("the floor")** — glass test: pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, check the **wall** specifically.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Briefly flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven. *(The probe job above does NOT replace this — it tests the permission layer on `complete_household_setup`, not the stranger path.)*
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit.** Glass test: approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

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
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.** See the shipped section above.
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable (#7) — FIXED 07-19, independently re-confirmed 07-21.**
- ✅ **Broad SECURITY DEFINER surface (#8) — CLOSED.**

**Real, open:**
- ⬜ **Activity-log forgery — actor label is client-supplied (NEW 07-21 late). REAL. NOT accepted-by-association.** `append_activity` takes `_actor_label text` as a parameter — the mechanism is confirmed in the function signature, not inferred. Intra-household and not a distribution blocker, **but the activity log is the receipt surface**: everything else in the accepted pile is "a kid could do a thing," while this is "a kid could make the record say an adult did a thing," which corrupts the surface an adult would use to catch the first thing. **Fix shape:** derive the actor server-side from `auth.uid()` rather than trusting the client payload. Small, contained, not urgent.
- ⬜ **Adult PIN plaintext in `localStorage` during signup (#5) — CONFIRMED, QUEUED. CREDIT-FREE.** Fix: collect it in post-confirmation finish-setup, don't persist client-side. Frontend-only → Code commit.
- ⬜ **Kids read `adults_only` reward names/costs (#6)** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together, in one audience-read pass.** Both tangle with the parked own-session-vs-per-member-auth decision. Fix them *with* that decision, not before, and not separately from each other.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit` to reproduce the scanner's "1 vuln." Low priority.

---

## ⬜ OPEN — from the 07-21 (evening) recurring-quest session

- ⬜ **`master-spec.md` fold — the recurring lifecycle + activeness model.** Design truth changed 07-21 and the spec has not been updated. Both models are in `decisions.md`. **Its own short session** — read the current spec, fold, replace. Deliberately not regenerated blind at session close.
- ⬜ **The same timezone seam likely exists in `handle_quest_approval`.** 07-21 fixed **creation only**. Not urgent, not yet observed; flagged so it isn't rediscovered from scratch.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** **Decide deliberately** rather than letting it stay accidental.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (evening): recurring-quest visibility, four layers deep

**Layer 1 — three definitions of "active," none shared. ✅ FIXED.** `board.tsx`, `Briefing.tsx` and `wall.tsx` each wrote their own active-quest predicate. Correct fix: one predicate, `isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts`, implementing `!archived && status !== 'approved' && (due_date == null || due_date <= today)`, imported by all three surfaces (`42fcddb`). **Both mechanisms are required and not redundant:** `status` hides completed-this-cycle; `due_date` hides not-yet-this-cycle.

**Layer 2 — the successor anchored to the wrong date. ✅ FIXED (migration).** `handle_quest_approval()` anchored the next instance to the *completed instance's* due date, so a late-completed weekly reappeared with no rest period. Re-anchored to `CURRENT_DATE` for weekly and monthly; daily untouched.

**Layer 3 — pre-migration ghost. ⬜ COSMETIC, OPEN.**

**Layer 4 — THE ACTUAL BUG: a timezone skew. ✅ FIXED, VERIFIED ON GLASS.** `due_date` defaulted to server `CURRENT_DATE` (UTC) but was judged against client-local today (Pacific), so evening-created quests landed dated "tomorrow" and were hidden by their own visibility filter. Fix: the create path stamps `due_date: todayIsoDate()` explicitly. **Verified on the glass in the exact failure window.**

**One phantom killed.** The "favorite-chip silently auto-assigns the assignee" bug **does not exist**; the create form has an explicit *Assign to* selector. Logged DECLINED.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Verdict-level record only — attack/exploit specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound (SECURE, denials shown live):** cross-tenant reads and writes; `current_family_id()` integrity; every household-scoped SECURITY DEFINER RPC; the `family_xp` view; the insert trigger; `system_flags` write.

**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` family-scoped; new `enforce_quest_family_refs` trigger. **Re-verified.**

**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` revoked from `authenticated`; anon/PUBLIC EXECUTE revoked on the two founder RPCs. **Re-verified 07-19, independently re-confirmed 07-21.**

**Teardown (a) done.** **Residual → Scott's dashboard cascade:** the two throwaway families (Alpha/Bravo) + their 3 auth users.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning)

**Wall display-mode fine-tune — verified on the physical glass.** Avatars enlarged (~18% of tile width). Quest-count line wrap killed (NBSP + `whitespace-nowrap` on both inner spans + `flex w-full min-w-0`). Two durable wall-rendering rules captured.

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
- ⬜ **`sandbox_exec` — unexplained role holding EXECUTE on every function in `public` (NEW 07-21 late).** Pre-existing, present before tonight's migration, so **not drift** — almost certainly a Lovable platform role. But it is a broad-execute role neither Scott nor jAIne can account for. **Ask Lovable what it is and what it's for**, so it isn't rediscovered mid-audit and mistaken for a breach. Recon, not alarm.
- ⬜ **PIN-plaintext-in-localStorage (#5)** — credit-free Code frontend commit.
- ⬜ **Rewards + quests audience reads (#6 and its quest twin)** — deferred, fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users — Scott's dashboard cascade. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies", the 07-21 test weeklies, and the ghost "Take out the trash" successor. **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. **The top structural blocker.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint** · **"Forgot PIN" confirm() copy** ("only the account owner" is inaccurate).

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
- **Enumerate before you triage.** *(New 07-21 late.)* A scanner that reports a *category* without naming instances cannot be triaged from the dashboard — the read is the only way to know whether it's noise or drift, and it's cheaper than the argument about it.
- **A defensive layer is not verified by the migration that adds it.** *(New 07-21 late.)* Fix → re-read the catalog → confirm on the live artifact.
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

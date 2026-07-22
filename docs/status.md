# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-21 (evening)** — *recurring-quest visibility, root-caused across four layers and fixed.* Started as "weekly quests aren't showing on the board" and ended three layers deeper than the opening symptom. **Four distinct defects, only the last of which was the one Scott walked in with:** (1) three surfaces each defined "active quest" independently and disagreed — unified behind a single imported `isActiveQuest` predicate; (2) the recurring successor's `due_date` anchored to the *completed instance's* old date, so a late-completed weekly reappeared immediately instead of resting — re-anchored to approval date via migration; (3) a pre-migration ghost successor still carries a stale future date (harmless, hidden from board, visible in Quest Log); (4) **the actual opening bug — a timezone skew.** `due_date` was stamped by the server in UTC and judged by the client in Pacific, so any quest created in the evening landed dated "tomorrow" and was hidden by its own visibility filter. Fixed at the create path; **verified on the glass in the failure window** (created → showed → completed → hid). One leg deliberately left open: the Monday reappearance test, staged and unfakeable tonight. **Two durable models captured to decisions.md** — the recurring lifecycle, and the same-clock date rule.

Last session (prior): **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit was RUN — the mortal-peril item, breached where it was weak, fixed, and re-verified closed on the live glass.* First time the gate got tested instead of reasoned about. Two throwaway households (Alpha, Bravo), real confirmed owner JWTs, every probe fired live against the deployed DB. **The isolation model is verified sound:** all cross-tenant reads/writes, `current_family_id()` integrity, every household-scoped SECURITY DEFINER RPC, the `family_xp` view, and the insert trigger — SECURE, denials shown. **Two breaches found — both grant drift, the recurring disease** — and both closed in one migration, then re-verified by re-firing the exact attacks with legitimate same-family paths confirmed still working. Finding A: `member_spendable()` wasn't family-scoped + quest FK columns accepted foreign-family profiles → cross-household ember read/tamper (now family-scoped + `enforce_quest_family_refs` trigger). Finding B: `families.is_founder` was owner-writable → self-serve paywall bypass (now service-role-only; folded in scanner #7's anon-execute revoke). Teardown (a) done; a small dashboard cascade remains. **The security distribution gate is cleared** — with one honest asterisk: grant-enumeration came back INCONCLUSIVE (the auth seat can't reach the catalog), so "every tested surface secure + both found breaches closed" is the accurate claim, not "grant surface provably clean." That last read is the Lovable SQL-dump method, and it does **not** block distribution. **The project flips from security-blocked to feature-and-polish.**

Last session (prior): **2026-07-19 (day)** — *Lovable's 8 scanner findings triaged end-to-end; P4×L8 reachability solved and the authenticated attack briefed.* Two of the three "Criticals" were our own safeguards holding (false positives). Reachability caveat resolved — Code self-provisions confirmed households via the browser tool. Two build-model decisions logged.

Last session (prior): **2026-07-19 (morning)** — *wall display-mode fine-tune, shipped on the physical iPad.* Avatars grew ~18%; the quest-count two-line wrap killed. Two durable wall-rendering rules logged. Theme-pack monetization hardened into **living-hold ambient themes** — parked.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate, a tenant-isolation model verified sound under live authenticated attack — and, as of 07-21, a recurring-quest lifecycle that is finally coherent end to end.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**The security gate — the thing everything was blocked behind — is cleared.**

> **P4×L8 RAN and the boundary held.** Two households, real JWTs, live attack. Isolation SECURE across every tested surface; two grant-drift breaches found and closed and re-verified. **One non-blocking residual:** grant-enumeration wasn't exhaustively readable from the auth seat — a Lovable SQL-dump read is the difference between "every tested surface secure" (true now) and "grant surface provably complete." Safe to distribute on what's verified; that read makes it airtight. *Caveats carried forward: live-schema-drift is real — both breaches were drift — so the grant read matters, and hand-applied DB changes remain forbidden.*

**The remaining structural gap is activation, not security.**

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. This is the top structural item before strangers.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **✅ P4×L8 tenant-isolation audit — SECURITY GATE CLEARED (2026-07-19).** Ran as a live authenticated two-household attack; boundary verified sound; two grant-drift breaches found, fixed, re-verified CLOSED. **Residual (non-blocking): the grant-enumeration read** via the Lovable SQL-dump method. Do it before wide distribution; it does not block the next builds. | — (was: Distribution / Gate B) |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix. **The top structural blocker.** | Gate D. Activation. |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen already received it. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). *The paywall it sells is now actually enforceable — the `is_founder` self-grant hole is closed.* | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready; gate mechanics built and secured. | — |

**Next-action fork (Scott's call):** the **grant-dump read** (cheap, closes the last security asterisk), the **auth email** (contained, first-impression), or **Pip onboarding** (the real activation gap). Plus, cheap and now overdue: the **master-spec fold** for the recurring lifecycle (see below).

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (evening): recurring-quest visibility, four layers deep

*Opening symptom: "weekly quests aren't showing on the board, but they show on the wall." Four distinct defects underneath it.*

**Layer 1 — three definitions of "active," none shared. ✅ FIXED.** `board.tsx` (member detail), `Briefing.tsx` (roster badge), and `wall.tsx` (roster + popup) each wrote their own active-quest predicate. The detail screen alone carried a server-side `.lte("due_date", today)`, so it under-counted against the badge that sat right next to it (May: badge 4 / detail 3; SnowDad: badge 1 / detail 0). First pass aligned all three on `archived=false + status≠approved` (`36544cc`) — which fixed the disagreement and **unhid every future-dated successor**, a regression caught immediately. Correct fix: one predicate, `isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts`, implementing `!archived && status !== 'approved' && (due_date == null || due_date <= today)`, imported by all three surfaces (`42fcddb`). **Both mechanisms are required and they are not redundant:** `status` hides completed-this-cycle; `due_date` hides not-yet-this-cycle. Either alone is 50% right — which is precisely why this bug survived three passes.

**Layer 2 — the successor anchored to the wrong date. ✅ FIXED (migration).** `handle_quest_approval()` computed the next instance's `due_date` as `date_trunc('week', NEW.due_date) + 1 week` — anchored to the *completed instance's* own due date, not to approval time. Complete a weekly **late** (its due_date already a period old) and "+1 period" lands in the **current** period, so the successor is due today-or-past and reappears immediately with no rest week. Re-anchored to `CURRENT_DATE` (approval day) for weekly and monthly; daily branch untouched. Landed as a migration via Lovable, auto-accept OFF.

**Layer 3 — pre-migration ghost. ⬜ COSMETIC, OPEN.** "Take out the trash" spawned its successor under the old formula and still carries a stale future `due_date`. Correctly hidden from the board by `isActiveQuest`, still visible in Quest Log (which applies no due_date filter). Real, harmless, needs a one-row reconcile whenever convenient.

**Layer 4 — THE ACTUAL BUG: a timezone skew. ✅ FIXED, VERIFIED ON GLASS.** `create.tsx` never sets `due_date`; the column defaults to `CURRENT_DATE`, **evaluated in the server's UTC**. But `isActiveQuest` judges it against the **client's local today** (`todayIsoDate()`, Pacific). Create a quest in the evening in Reno and the server is already on tomorrow's date → the quest lands due "tomorrow" → its own visibility filter hides it. Every symptom fit: visible in Quest Log, absent from the board, "created just now" yet not active. **Fix:** the create path stamps `due_date: todayIsoDate()` explicitly, so the date is written and judged in the same clock. Shipped via Lovable. **Verified on the glass in the exact failure window** — weekly created at night showed on the assignee's board immediately, was completed, was approved, and disappeared.

**Two durable models captured** (decisions.md): the recurring-quest lifecycle, and the same-clock date rule.

**One phantom killed.** jAIne repeatedly flagged a "favorite-chip silently auto-assigns the assignee" bug across several turns. **It does not exist.** The create form has an explicit *Assign to* selector (Open to anyone / a named member); Scott was deliberately assigning every test quest. Logged DECLINED so no future instance re-flags a working feature as a defect.

---

## 🟡 PENDING VERIFY

- 🟡 **Monday reappearance test — the recurring lifecycle's one unfakeable leg (STAGED).** A completed weekly is sitting in the hold right now. On **Monday 2026-07-27**, open the board and confirm it reappears open and actionable. This verifies both the re-anchored successor date and the no-scheduler reappearance path (`isActiveQuest` surfaces it the first time the board loads on-or-after its due date). *Nothing to do until Monday — just look.*
- 🟡 **`handle_quest_approval` anchor migration — landed but not independently confirmed on `origin/main`.** Believed applied (the "Take out the trash" ghost behaves as a pre-migration row, which implies it). Monday's test is its real verification; if Monday fails, confirm the migration landed **before** re-diagnosing.
- 🟡 **Avatar render fallback ("the floor")** — glass test: pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, check the **wall** specifically.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Briefly flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF. A mis-tag is a silent revenue leak or locked-out freebie.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** `RECURRENCE_LABEL` enrichment scoped, skipped. Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit.** Glass test: approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device; confirm the wall never exposed a commit control it shouldn't.

---

## ⬜ OPEN — from the 07-21 recurring-quest session

- ⬜ **`master-spec.md` fold — the recurring lifecycle + activeness model.** Design truth changed 07-21 and the spec has not been updated. Both models are in `decisions.md`; the spec needs them folded into the Quest section. **Its own short session** — read the current spec, fold, replace. Deliberately not regenerated blind at session close.
- ⬜ **The same timezone seam likely exists in `handle_quest_approval`.** The successor's date math uses server `CURRENT_DATE` / `date_trunc` — the same UTC-vs-local skew that broke creation could nudge an anchor by a day at the edges. 07-21 fixed **creation only**. Not urgent, not yet observed; flagged so it isn't rediscovered from scratch.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; it's correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** It shows future-dated successors the board correctly hides, which is what made this session's diagnosis confusing. Arguably correct for an oversight surface ("every quest in your hearth"), arguably a leak. **Decide deliberately** rather than letting it stay accidental.

---

## 🟢 SECURITY TRIAGE — Lovable's 8 scanner findings — DONE 2026-07-19; statuses post-audit

*Verdict-level only. Mechanism lives in the Code session, never here.*

**False positives — safeguards held (vindicated):**
- ✅ **"Forgot PIN" takeover (was CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: confirm() copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (was CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`.
- ✅ **Broad SECURITY DEFINER surface (#8) — CLOSED.** Sensitive definer RPCs anon-denied/internally guarded (re-confirmed live in the audit).

**Real — now resolved or queued:**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable (#7) — FIXED.** Folded into the P4×L8 fix bundle. Re-verified denied.
- ⬜ **Adult PIN plaintext in `localStorage` during signup (#5) — CONFIRMED, QUEUED. CREDIT-FREE.** Fix: collect it in post-confirmation finish-setup, don't persist client-side. Frontend-only → Code commit.
- ⬜ **Kids read `adults_only` reward names/costs (#6) — CONFIRMED, DEFERRED (on purpose).** The real fix tangles with the parked own-session-vs-per-member-auth decision. Fix it *with* that decision, not before.

**Known-accepted boundary (re-confirmed by the audit):**
- 🔵 **Tier boundary is frontend-only in the shared-session model (#4) — KNOWN-ACCEPTED.** A device-kid rides the owner's ambient parent JWT. **Intra-household, not cross-tenant.** The own-session-vs-per-member-auth fork it raises is parked.

**Quest UPDATE / trigger (#3):** cross-tenant facet SECURE; the intra-household `a_enforce_quest_update_authority` trigger governs kid-vs-parent, which is the accepted walk-up boundary.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit` to reproduce the scanner's "1 vuln." Low priority.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Verdict-level record only — attack/exploit specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound (SECURE, denials shown live):** cross-tenant reads (families, profiles, quests, rewards, redemptions, events, lists, list_items, activity_log — all zero foreign rows); cross-tenant writes (RLS-denied); `current_family_id()` integrity; every household-scoped SECURITY DEFINER RPC; the `family_xp` view (`security_invoker=true`); the insert trigger (`enforce_profile_insert_rules` attached live); `system_flags` write (SELECT-only for authenticated).

**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` wasn't family-scoped and quest FK columns accepted foreign-family profiles. **Fix:** `member_spendable()` scoped to `current_family_id()`; new `enforce_quest_family_refs` trigger. **Re-verified** both directions.

**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` was owner-writable (grant drift). **Fix:** revoke `UPDATE(is_founder)` from `authenticated`; + revoke `anon`/`PUBLIC` EXECUTE on the two founder RPCs (**folds in scanner #7**). **Re-verified.**

**Fix delivery:** one bundled migration (Lovable, auto-accept OFF), landed as a versioned migration file.

**Teardown (a) done.** **Residual → Scott's dashboard cascade:** the two throwaway families (Alpha/Bravo) + their 3 auth users, including the orphan `+bravo` unconfirmed user. Identifiers kept out of this public repo.

**Open slice (non-blocking): grant-enumeration — INCONCLUSIVE.** → Lovable SQL-dump method. Both found breaches were grant drift, so this is the honest last step to "provably clean." Does not block distribution.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning)

**Wall display-mode fine-tune — verified on the physical glass.**
- ✅ **"Who's on the Hearth" avatars enlarged** (~18% of tile width; ring offset + padding tightened).
- ✅ **Quest-count line wrap killed.** Root cause: `whitespace-nowrap` lost across the flex-item boundary. Fix: NBSP + `whitespace-nowrap` on both inner spans + `flex w-full min-w-0`.
- **Two durable wall-rendering rules captured** (decisions.md).

---

## 🟢 SHIPPED / RESOLVED — 2026-07-18 (evening)

**Avatar transport — shipped end-to-end.** 48-roster into `src/assets/avatars/`, committed, synced. Tier-tagged free/founder (16/32). `families.is_founder` added. Global gate as a DB value (`system_flags.founder_gate_enabled`, seeded false) — **flip is a one-line UPDATE, not a build.** Picker shows all 48; founder tiles lock only when gate ON and household not founder. Re-forge: 28/28 invalidated, run live.

**QA burn-down — three shipped, verified:** Ranks per-member completed-quest expand; calendar month view; wall "hero" vocab leak killed. **QA #5 reclassified** as cross-hold super-admin / tier-2 support → distribution-era.

---

## ⬜ OPEN FINDINGS — from the avatar session

- ⬜ **Re-forge reach across the 13 households (real defect, later fix).** Re-forge only prompts the member who logs in; others render the fallback placeholder until an adult manually fixes each. Matters when re-forge reaches the 13.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." Verify and align.

---

## 🟢 SHIPPED — 2026-07-18 (day)

**Doc-protocol repair + catch-up committed.** 07-17's over-correction reconciled as never-canon; six 07-17 decisions committed; sync-to-origin rule folded into `session-protocol`.

**SnowDad Vault spendable = 0 — cleaned (data, not code).** **Two findings logged:** `parent_self_redeem` doesn't write `activity_log` (NOTED); `member_spendable` silently clamps overdraw to 0 (NOTED — the clamp-hiding facet remains open).

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Grant-enumeration read (the one open P4×L8 slice) — Lovable SQL-dump method.** Non-blocking; the honest last step to "grant surface provably clean." ⚠️ *Findings detail stays in the Code/Workstream session.*
- ⬜ **PIN-plaintext-in-localStorage (#5)** — credit-free Code frontend commit.
- ⬜ **Rewards audience (#6)** — deferred, fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users — Scott's dashboard cascade. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies". **Pattern: deleting a quest does not clean up redemptions it funded.** *(Add: the 07-21 test weeklies — "testing weekly," "really testing weekly," "really REALLY testing weekly" — and the ghost "Take out the trash" successor.)*

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. **The top structural blocker.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Re-forge reach across the 13** · **Own-session vs per-member-auth** (rewards-audience #6 waits on it) · Favorites on the wall · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. The own-session-vs-per-member-auth fork is parked. See `decisions.md`.

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.** Live-schema-drift is a 5×-observed pattern.
- **A Code job isn't done until the artifact is observable from outside the agent.** Re-confirmed hard 07-21: Code reported a fix complete and typecheck-clean while the commit sat unpushed on a local clone — invisible to Lovable and the live site. **Verify `origin/main`, not the agent's report.**
- **Recon before build, every time.** 07-21 cost three wrong diagnoses in a row where jAIne reasoned from screenshots instead of reading the code. Every one collapsed the moment recon ran.
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."**
- **Fix → re-fire the exact attack → verify denied on the live artifact → then teardown.**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member. *(Re-confirmed 07-19 twice.)*

---

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster "no members" root-caused to zero Data-API grants for `authenticated` (a live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). *(Also proved Code can browser-provision confirmed test households end-to-end.)*

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

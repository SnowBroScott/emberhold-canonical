# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-25** — *a greenfield product session that ended by folding a whole second app back into Emberhold, and left one real security finding behind it.* Started as "name a fitness app," ended with **the four avatar registers reinterpreted as the four child modules of Emberhold** and the standalone sibling-app frame killed. **Net code delta to Emberhold: one column, one RPC, one unrouted screen, one tested pure-TS library.** Net *understanding* delta: much larger — see DECISIONS 07-25. **Along the way an entire parallel tenancy (`holds` / `hold_accounts` / `members` + five training tables) was built inside the production project and then removed the same night**, empty and never reachable from navigation. **And the accidental find that matters most: `anon` held full CRUD on `families`, including UPDATE on `is_founder`** — the column LOCKED service-role-only on 07-19. RLS was the only thing denying it. Revoked. **This is the sixth grant-drift instance and it re-prices the table-grant read from "cheap asterisk" to front-of-queue.**

Last session (prior): **2026-07-23** — *the household-local date seam: the daily-quest bug root-caused to a different function than anyone expected, then fixed as a class rather than an instance.* Started as "dailies have the same problem weeklies had" and ended by generalizing the 07-21 same-clock rule. **The create path was innocent** — `create.tsx` does not branch on recurrence and stamps `due_date: todayIsoDate()` for all four types. jAIne's confident opening hypothesis (a daily branch overriding the stamp at insert) was **wrong**; the recon brief was written to disprove it rather than confirm it, which is the only reason it was caught in one pass. **The actual mechanism:** `roll_missed_dailies()` fires unconditionally on every `/board` mount, and `create.tsx` navigates to `/board` on success — so the RPC ran seconds after creation, judged the fresh row against server UTC (`due_date < CURRENT_DATE`), declared it "missed," archived it, and respawned it dated tomorrow. Correct for one round-trip, then clobbered. **The enumeration is what made the session worth it:** a full grep of `supabase/` for household-local date math found **six live violations across two functions**, not one — `roll_missed_dailies()` (filter + respawn) and `handle_quest_approval()` (daily, weekly, monthly, and fallback successor anchors, all four). **The fix shape changed mid-session because `handle_quest_approval` is a trigger** — no caller, no place to pass a client-supplied date. That structurally killed the client-stamp approach and forced the right answer: `families.timezone` + a `household_today(fam)` helper, both functions deriving from it. **One migration, zero frontend changes, and the client-supplied-date trust-widening evaporated with it.** Catalog re-read confirms no `CURRENT_DATE` remains in any expression touching `due_date`. **Create-path leg verified on the glass. Two legs still unverified.**

Last session (prior): **2026-07-21 (late)** — *the SECURITY DEFINER grant surface: enumerated, root-caused, fixed, and re-verified clean. The P4×L8 function-grant asterisk is closed.* **Root cause found and it is structural, not incidental:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function, so every SECURITY DEFINER function Emberhold has ever shipped arrived anon-callable unless someone remembered to revoke. Seven functions were carrying it. **Fixed as one grant-then-revoke migration.** **Re-read verified: zero `PUBLIC`/`anon` grants on any SECURITY DEFINER function in `public`.**

Last session (prior): **2026-07-21 (evening)** — *recurring-quest visibility, root-caused across four layers and fixed.* Three surfaces each defined "active quest" independently — unified behind a single imported `isActiveQuest` predicate; the recurring successor's `due_date` anchored to the completed instance's old date — re-anchored via migration; a pre-migration ghost successor still carries a stale future date; and a timezone skew at the create path, fixed and verified on the glass.

Last session (prior): **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit was RUN — the mortal-peril item, breached where it was weak, fixed, and re-verified closed on the live glass.* **The isolation model is verified sound.** Two breaches found — both grant drift — and both closed in one migration, then re-verified. **The security distribution gate cleared**, with a grant-enumeration asterisk — *closed for functions (07-21 late); **still open for tables, and now urgent (07-25)**.*

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate, a tenant-isolation model verified sound under live authenticated attack, a function-grant surface that is provably clean, a quest date model deriving household-local time from one named server-side function — and, as of 07-25, a household-level module-enablement column with no consumer yet.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**Emberhold is now a four-module product on paper and a one-module product in code.** The four avatar registers were reinterpreted 07-25 as the four functional layers a household actually runs: **FORGE = training · KEEP = the house maintained · GARDEN = growing · HALL = cooking and feeding.** Only Household ships today. **Training is the first module to be built, and nothing of it exists in the codebase** — the 07-25 prototype was removed the same night.

> **The remaining structural gap is still activation, not security — and it just absorbed a second job.** Onboarding ends at setup, not at activation: a new household lands on a board with no doorway to the first quest. **As of 07-25 that same build also owns module intent** — the empty-board doorway and the "what does this Hold want out of the app" question are one screen, not two. See CRITICAL PATH #2.

> **The table-grant surface has not been re-read since the 07-16 outage, and 07-25 proved it is not clean.** A single accidental sample (`families`) came back with `anon` holding full CRUD including UPDATE on `is_founder`. See CRITICAL PATH #1a.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **✅ P4×L8 tenant-isolation audit — SECURITY GATE CLEARED (2026-07-19).** Boundary verified sound; two grant-drift breaches found, fixed, re-verified. **Function-grant residual CLOSED 07-21 late.** | — |
| **1a** | **🔴 TABLE-GRANT READ — RE-PRICED 07-25, NOW THE TOP SECURITY ITEM.** Was "cheap, non-blocking, closes an asterisk." One accidental sample found `anon` with full CRUD on `families` including `is_founder`. Tables have not been re-read since the 07-16 grant outage. **Read every table's grants for `anon` and `authenticated`, revoke anything unearned.** | Gate B honesty. |
| **2** | **Pip first-run onboarding + empty-board doorway + module intent.** **The top structural blocker, and as of 07-25 it carries three payloads, not two.** | Gate D. Activation. |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Next-action fork (Scott's call, stated 07-25):** either **the security gap (1a)** or **more Forge**, with onboarding (2) as the third door. Scott's own framing: *onboarding will need to take into account the potential/likely other layers.* Still owed and cheap regardless: the **Clean Toys discriminator** (30 seconds), the **overnight rollover check** (next morning board load), and the **master-spec fold** (now FOUR items deep and the oldest debt on the board).

---

## 🟢 SHIPPED / RESOLVED — 2026-07-25: the module reframe

**What actually landed in Emberhold's codebase — the whole delta, and it is small:**

- ✅ **`families.enabled_modules text[] NOT NULL DEFAULT ARRAY['household']`** — household-level module enablement. All existing households backfilled to `['household']`; behavior unchanged for all 13.
- ✅ **`set_enabled_modules(_modules text[])`** — SECURITY DEFINER, `search_path` pinned, EXECUTE to `authenticated` only, PUBLIC and anon revoked. Adult-gated, validates against the allowed set, forces `household` to always be present, writes only the caller's own family row.
- ✅ **No new grant on `families`.** Deliberate and load-bearing: `authenticated` retains column-level UPDATE on `name` **only**. `enabled_modules` and `is_founder` are both `false`. The RPC exists specifically so the paywall column never had to become writable to add a feature column.
- ✅ **`/setup/intent`** — an unrouted, unlinked screen. Reachable by direct URL only. **Nothing reads `enabled_modules` yet** — this release records intent and gates nothing.
- ✅ **`src/lib/progression.ts` + `progression.test.ts`** — a pure-TypeScript strength-training progression engine (RPE-driven autoregulation, plate-snapping against an equipment inventory, Epley+RIR e1RM). **10/10 tests passing, zero Supabase imports, zero network.** Retained deliberately through the teardown; it is table-agnostic and will be reused when the Forge module is built for real.

**What was built and then removed the same night.** Lovable carried context forward from a separate greenfield project and constructed a **complete parallel tenancy inside Emberhold's production database**: `holds`, `hold_accounts`, `members`, plus `exercises`, `equipment_inventory`, `prescriptions`, `training_sessions`, `set_logs`, and the functions `current_hold_ids()` and `provision_forge()`. **All eight tables held zero rows except `holds`/`hold_accounts` at one throwaway row each. No hearthmate data ever entered `members`. Neither new route was reachable from navigation; `_authenticated/route.tsx` was untouched; `/board` never degraded.**

**Two failure modes, both worth keeping:**
1. **jAIne wrote two full Lovable prompts and a complete schema without fetching a single canonical doc.** The fetch protocol exists precisely to prevent this. The invented noun `holds` — a *third* name for a tenant that is already `families` — is what the protocol would have caught in the first thirty seconds.
2. **jAIne then ratified Lovable's unbriefed scope.** After the prototype appeared inside Emberhold, jAIne wrote *"leave the Exercises and Equipment sections as they are, those belong here"* — treating an explicit DO-NOT-BUILD violation as intent, without knowing what tables were underneath. **The prompt's negative constraints were correct and were simply not enforced by anyone.**

**Recon before teardown.** A read-only live-schema recon (`information_schema` / `pg_catalog`) enumerated every object, every grant, every policy, and every registered route before anything was dropped. **Note the recon's own caveat, which was material:** Postgres records no DDL timestamps, so recency was inferred from OID ordering — which over-reported. Four functions flagged as "created today" (`household_today`, `enforce_quest_family_refs`, `my_household_is_founder`, `founder_gate_enabled`) are documented 07-19/07-23 objects. **An OID heuristic dates objects relative to each other, never absolutely.**

**Revert was considered and rejected on evidence, not preference.** Lovable's version history restores code and project files only — it does **not** roll back applied migrations, and it **removes migration files while leaving their objects live**, putting tracked history out of sync with the database. Given schema-history drift is already a named 5×-observed landmine here, a revert would have manufactured a sixth instance to clean up a fifth. Forward migration instead.

**The teardown.** One migration: eight `DROP TABLE` in FK order (no `CASCADE`, deliberately — an unexpected dependency should stop the migration, not be forced through), `DROP FUNCTION provision_forge`, `DROP FUNCTION current_hold_ids`, and `/lift` deleted. **Verified:** all eight tables gone, both functions gone, `enabled_modules` and `set_enabled_modules()` present with grants unchanged, `progression.test.ts` 10/10, `/board` HTTP 200.

**🔴 The security finding, which is the most valuable thing the session produced and had nothing to do with it.** The recon read grants on `families` and found: **`anon` holding SELECT, INSERT, UPDATE, DELETE at table level, plus UPDATE on every column including `is_founder`.** Master-spec LOCKED `is_founder` as service-role-write-only on 07-19 — that rule is what makes the Founding Guildhall paywall enforceable. **Not exploitable as it stood** (no anon-permitting RLS policy on `families`), but defense-in-depth was gone and the grant surface directly contradicted a LOCKED rule.

- **Preflight before revoking, and it mattered:** confirmed `/join?code=` only stashes the code and hands to `/auth`; all family lookup runs through the SECURITY DEFINER RPCs `find_family_by_code` / `complete_household_setup`. **No anon reads of `families` anywhere.** Safe to revoke.
- **Revoked:** all table and column grants on `public.families` from `anon`. **Verified: 0 table grants, 0 column grants.**
- **`current_hold_ids()` also carried an anon EXECUTE grant** — moot, dropped with the function, but it is the seventh instance of the same default-grant mechanism.
- **This is grant-drift instance six**, and unlike 07-21's function sweep it is on the *table* surface — the organ that has not been read since 07-16.

---

## 🟡 PENDING VERIFY

- 🟡 **Daily rollover across midnight (07-23) — THE LEG THAT MATTERS. STILL OPEN.** `roll_missed_dailies` is the archive-and-respawn path and **both** its filter and its write changed. The failure mode is not a wrong date — it is dailies **silently never rolling at all**. **First board load of the morning: confirm yesterday's daily is gone and a fresh one is open and dated today.**
- 🟡 **Monday reappearance test (STAGED, 2026-07-27) — carrying two payloads.** Verifies the 07-21 anchor logic **and** `household_today()` in trigger context simultaneously. **If it fails there are two candidates, not one.** *Nothing to do until Monday — just look.*
- 🟡 **Fourth-activeness-surface discriminator (07-23) — 30 seconds, still not done.** See OPEN below.
- 🟡 **`/setup/intent` and `/board` render check (NEW 07-25).** `/board` returned HTTP 200 and the Briefing was seen rendering correctly mid-session. `/setup/intent` has **never been loaded by a human**. **200 is not "renders"** — eyeball both before building on either.
- 🟡 **Grant-revoke verification probe job (07-21 late) — BRIEF DRAFTED, DEFERRED BY SCOTT.** The grants are confirmed correct *on paper*; nothing has confirmed they didn't break a live path. **The whole job is one distinction:** `SQLSTATE 42501, permission denied for function` = the grant is broken (FAIL). Any application-level error = the call reached the function body (PASS). Probe all six revoked RPCs plus three untouched controls (`current_family_id`, `member_spendable`, `approve_redemption`), real authenticated JWT, non-existent UUIDs, no writes. **Sonnet · auto-accept OFF · read-only.** *(07-25: the `families` anon revoke should be added to this probe's scope.)*
- 🟡 **`handle_quest_approval` anchor migration (07-21) — landed but not independently confirmed on `origin/main`.**
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

## ⬜ OPEN — the module layer (NEW 07-25)

- ⬜ **🔴 `enabled_modules` has no consumer.** The column and its RPC exist; nothing reads them, no screen is gated, `/setup/intent` is unlinked. **This is deliberate and it is also debt** — an unread config column is exactly the kind of thing that gets rediscovered in three months and mistaken for dead code. Either wire it (with onboarding, CRITICAL PATH #2) or delete it. **Do not let it sit unowned.**
- ⬜ **The three currency/membrane amendments are `proposed LOCKED`, not LOCKED.** See `decisions.md` 07-25. **Until Scott sets status, the LOCKED principles in `master-spec.md` still govern as written** — one currency, game-in-quests-only, free-is-a-full-tool. Any Forge build that assumes the amendments is building on an undecided foundation.
- ⬜ **Currency shape for the Forge is UNRESOLVED and collides with a LOCKED principle.** "Sparks" as a Forge-local currency contradicts *"Two backbones: one currency (embers)."* Options are (a) training mints embers, (b) amend two-backbones to allow sealed per-module economies. **Not a naming question — an architecture question.** See `parking-lot.md` OPEN DECISIONS.
- ⬜ **The Forge/register naming collision is live.** `members.class` already carries `FORGE` as an avatar/animation value. A *module* also called the Forge is ambiguous inside the same app and the same schema. The 07-25 prompt deliberately used the neutral string `training` in `enabled_modules` to avoid hard-coding an undecided call. **Decide the user-facing label before it ships anywhere visible.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** Role is not the same as training participation; a twelve-year-old lifting in a garage gym is normal. When it breaks it is a per-member flag, not a role check. **Do not build now.**
- ⬜ **Client-facing training (paying clients, not hearthmates) is deliberately unresolved.** A client is not a hearthmate and cannot share a household tenant. If it ever happens it is a separate trainer-facing surface reading the same data, not a change to the household model. **Not a Gate-A/B/C/D/E item.**

## ⬜ OPEN — carried from 07-23

- ⬜ **🔴 POSSIBLE FOURTH ACTIVENESS SURFACE — DISCRIMINATE FIRST, IT'S ONE QUESTION.** "Clean Toys Downstairs" (daily, IN PROGRESS, assigned to Mia) appeared in the Quest Log but **not** in Mia's roster/profile "active quests" list. **Expand it in the Quest Log and read its due date.**
  - **Future-dated → pre-fix debris.** Nothing is wrong. Delete it and move on.
  - **Today or earlier → a fourth surface carrying its own predicate.** The 07-21 unification swept `board.tsx`, `Briefing.tsx`, `wall.tsx`. The member profile/roster view was **not** in that list. That would make the LOCKED activeness decision partially untrue as written.
  - **Why it outranks its size:** the harm is a kid claiming a quest and not seeing it on her own profile — the "where'd my chore go" failure, aimed at the person the app exists to be fun for.
- ⬜ **STALE chip may be a fourth read of `due_date`.** In the Quest Log the weekly wears a **STALE** badge and the dailies do not. Verify, then fold or leave deliberately.
- ⬜ **Household timezone capture at setup — a Gate B item.** `families.timezone` defaults to `America/Los_Angeles`, correct for all 13 current households and **silently wrong for the first stranger in Ohio**. Fix is small and frontend-only: capture `Intl.DateTimeFormat().resolvedOptions().timeZone` during household setup. **Belongs with Pip onboarding** — which as of 07-25 also owns module intent, so that screen now carries three jobs.
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** Logged so it is not rediscovered as an oversight.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?** **Decide deliberately.** *(Currently load-bearing as a diagnostic surface — it's how both date bugs were confirmed.)*
- ⬜ **`master-spec.md` fold — NOW FOUR ITEMS DEEP AND THE OLDEST DEBT ON THE BOARD.** Owes: the recurring lifecycle, the activeness model (both 07-21), the household-local date rule (07-23), and **the module architecture + whichever 07-25 amendments Scott locks**. **Its own short session** — read the current spec, fold, replace. Deliberately not regenerated blind at session close, three times now.

---

## 🟢 SECURITY TRIAGE — statuses as of 2026-07-25

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated 07-21 late, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: `confirm()` copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`. *(07-25: re-confirmed from a second angle — the anon-revoke preflight independently verified the join path never reads `families` with the anon key.)*
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.** Intra-household, not cross-tenant. **Revisit if the own-session-vs-per-member-auth fork is decided.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** `wall_request_redemption`; the wall proposes and never commits. **Same revisit trigger.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.** Structurally unclearable.

**Fixed:**
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable — FIXED 07-19, re-confirmed 07-21.**
- ✅ **`anon` full CRUD on `families` incl. `is_founder` (NEW, FIXED 07-25).** Table + column grants revoked; verified zero. **Directly contradicted a LOCKED rule. Instance six of grant drift.**

**Real, open:**
- ⬜ **🔴 TABLE-GRANT READ — now the top security item.** See CRITICAL PATH #1a.
- ⬜ **Activity-log forgery — actor label is client-supplied.** `append_activity` takes `_actor_label text` as a parameter. **The activity log is the receipt surface**: everything else in the accepted pile is "a kid could do a thing," while this is "a kid could make the record say an adult did a thing," which corrupts the surface an adult would use to catch the first thing. **Fix shape:** derive the actor server-side from `auth.uid()`.
- ⬜ **Adult PIN plaintext in `localStorage` during signup — CONFIRMED, QUEUED. CREDIT-FREE.** Collect it in post-confirmation finish-setup; don't persist client-side.
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together, in one audience-read pass**, *with* the own-session-vs-per-member-auth decision.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. Low priority.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-23: the household-local date seam

**The symptom.** A newly created quest with `recurrence = daily` did not appear on the board. It appeared in the Quest Log carrying a `due_date` of tomorrow.

**The wrong hypothesis, named because the process caught it.** jAIne's confident opening call was that the create path branched on recurrence and overrode the 07-21 `todayIsoDate()` stamp. It does not. **The recon brief was written to force a ruling between two named alternatives rather than to confirm the hypothesis. That framing is why one recon pass found the real mechanism.**

**The real mechanism.** `board.tsx` fires `roll_missed_dailies` unconditionally on every mount; `create.tsx` navigates to `/board` on successful insert. The RPC ran seconds after creation, judged the fresh row against server UTC, archived it, and respawned it dated tomorrow.

**The enumeration.** A full grep of `supabase/` found **six live violations across two functions** — `roll_missed_dailies()` (2 sites) and `handle_quest_approval()` (4 sites). Frontend: zero.

**What shipped.** `families.timezone` (IANA, NOT NULL, default `America/Los_Angeles`), `household_today(fam uuid)` STABLE, all six sites replaced, both signatures unchanged, zero frontend changes.

**Verified.** Catalog re-read: no `CURRENT_DATE` in any expression touching `due_date`. **Glass: a daily created post-migration appears on the board and in the member roster immediately.**

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (late): the SECURITY DEFINER grant surface

**✅ Row 0029 — PERMANENTLY IGNORED.** A definer RPC callable by authenticated users *is* the architecture.

**✅ Row 0028 — REAL, ROOT-CAUSED, FIXED.** **Seven functions carried `PUBLIC` + `anon`.** **Root cause:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function. The affected set was chronological, not random — `enforce_quest_family_refs` was created **inside the 07-19 grant-drift fix bundle itself**. **Every revoke this project has ever done was a reactive one-off against a mechanism that re-breaks the default automatically.** **The fix:** one migration, **grant-then-revoke in that order**. **Re-verified** by full catalog re-read.

**✅ The 07-19 founder revoke held.** **✅ The ignored-issues list validated — five for five**, but for *three different reasons*, and two only because the shared-session model is what it is.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (evening): recurring-quest visibility

**Layer 1 — three definitions of "active," none shared. ✅ FIXED.** `isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts`, imported by `board.tsx`, `Briefing.tsx`, `wall.tsx` (`42fcddb`). *(07-23: possibly not a complete sweep — see OPEN.)*
**Layer 2 — successor anchored to the wrong date. ✅ FIXED (migration).**
**Layer 3 — pre-migration ghost. ⬜ COSMETIC, OPEN.**
**Layer 4 — THE ACTUAL BUG: a timezone skew. ✅ FIXED, VERIFIED ON GLASS.**
**One phantom killed** — the "favorite-chip auto-assigns" bug does not exist. Logged DECLINED.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Verdict-level record only — attack specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound:** cross-tenant reads and writes; `current_family_id()` integrity; every household-scoped definer RPC; the `family_xp` view; the insert trigger; `system_flags` write.
**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` family-scoped; `enforce_quest_family_refs` trigger added.
**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` revoked from `authenticated`. *(07-25: the `anon` half of the same column was still open until tonight.)*
**Residual → Scott's dashboard cascade:** the two throwaway families (Alpha/Bravo) + their 3 auth users.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning) / 07-18

**Wall display-mode fine-tune — verified on the physical glass.** Avatars enlarged (~18% of tile width). Quest-count line wrap killed.

**Avatar transport — shipped end-to-end.** 48-roster into `src/assets/avatars/`, committed, synced. Tier-tagged free/founder (16/32). Global gate as a DB value (`system_flags.founder_gate_enabled`, seeded false) — **flip is a one-line UPDATE, not a build.** Re-forge: 28/28 invalidated, run live.

**Doc-protocol repair + catch-up committed.** Sync-to-origin rule folded into `session-protocol`.

- ⬜ **Re-forge reach across the 13 (real defect, later fix).** Re-forge only prompts the member who logs in; others render the fallback placeholder until an adult manually fixes each.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." Verify and align.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **🔴 TABLE-grant read — see CRITICAL PATH #1a.** Function grants are provably clean as of 07-21 late; **tables have not been re-read since the 07-16 outage**, where fourteen tables lost their `authenticated` Data-API grants — and 07-25 found the opposite failure (anon grants too *wide*) on the one table it happened to look at. Same disease, both directions. ⚠️ *Findings detail stays in the Code/Workstream session.*
- ⬜ **Grant-revoke verification probe job** — see PENDING VERIFY. Brief drafted 07-21, deferred. **Add the 07-25 `families` anon revoke to its scope.**
- ⬜ **Activity-log actor forgery** — derive actor server-side from `auth.uid()`.
- ⬜ **`sandbox_exec` — unexplained role holding EXECUTE on every function in `public`.** Pre-existing, so **not drift** — almost certainly a Lovable platform role. **Ask Lovable what it is and what it's for.** Recon, not alarm.
- ⬜ **PIN-plaintext-in-localStorage** — credit-free Code frontend commit.
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Household timezone capture at setup** — Gate B item; belongs with Pip onboarding.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies", the 07-21 test weeklies, the ghost "Take out the trash" successor, and **the 07-22/23 pre-fix test dailies — these carry future `due_date` values and will keep presenting as a failed fix every time this area is tested.** **Pattern: deleting a quest does not clean up redemptions it funded.** *(07-25: the throwaway `holds`/`hold_accounts` rows went with their tables — nothing added to this list.)*

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding + empty-board doorway + module intent.** **The top structural blocker, now three payloads.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint** · **"Forgot PIN" confirm() copy** · **STALE chip predicate**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The three downstream modules (Keep / Garden / Hall)** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Re-forge reach across the 13** · **Own-session vs per-member-auth** (rewards/quests audience reads wait on it — *and two ignored scanner findings revive if it's decided*) · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED 07-22) · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19, re-affirmed 07-21)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Two scanner findings are ignored *because of* this boundary**; deciding the own-session-vs-per-member-auth fork revives both. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **Fetch the canon before producing anything. (NEW 07-25 — the hardest-won line of the session.)** jAIne wrote a full schema and two build prompts from memory without fetching a single doc, and invented a third name for a tenant that already had one. **The protocol is not ceremony; it is the thing that catches an invented noun in thirty seconds instead of eight tables later.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing. (NEW 07-25.)** Lovable built an entire training stack that the prompt explicitly forbade. **Read what landed against what was briefed, every time — and never ratify unbriefed scope without reading the schema underneath it.**
- **An OID heuristic dates objects relative to each other, never absolutely. (NEW 07-25.)** Postgres records no DDL timestamps. Cross-reference against the docs before believing a "created today" list.
- **A code revert is not a database revert. (NEW 07-25.)** Lovable's version history restores files only, removes migration files while leaving their objects live, and manufactures schema-history drift. **Undo schema forward, always.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.** Live-schema-drift is now a 6×-observed pattern.
- **A Code job isn't done until the artifact is observable from outside the agent.** **Verify `origin/main`, not the agent's report.**
- **Recon before build, every time.**
- **Brief recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X.
- **Sweep the class before fixing the instance.** Seven functions carrying a grant default (07-21); six date sites across two functions (07-23); **and the one table anybody happened to read had wide-open anon grants (07-25) — which is an argument about the twenty that weren't read.**
- **Enumerate before you triage.**
- **A defensive layer is not verified by the migration that adds it.** Fix → re-read the catalog → confirm on the live artifact. **And a catalog read is not a glass test** — it proves the code is right, not that the behavior is. **HTTP 200 is not "renders," either (07-25).**
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member. *(Re-confirmed 07-19 twice; join path independently re-verified 07-25.)*

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster "no members" root-caused to zero Data-API grants for `authenticated` (a live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). **⚠️ This is the reason the table-grant read is still owed — it has not been re-read since, and 07-25 raised the stakes.**

## 🟢 SHIPPED — 2026-07-15

Live privilege-escalation in `complete_household_setup` fixed. Admit/deny enum crash fixed. `recurrence_day` prod crash fixed (`172a07f`). Pending-member roster leak fixed (`b31c92c`).

## ✅ SHIPPED — 2026-07-14

Admit-on-approval data layer. Hub open-bounty count unified. Recurrence reworked to fixed calendar anchors.

## ✅ SHIPPED — 2026-07-12

Claude Code proven as a build lane. Auth hardening. Hold Alpha built (the P4×L8 instrument). 13 real accounts confirmed.

## ✅ SHIPPED — 2026-07-11

Engine daily respawn verified. Vocabulary cleanup. Safe-area inset fix. Avatar roster regenerated (48 characters, 19 sheets). Lovable ↔ GitHub sync connected.

## ✅ SHIPPED — 2026-07-10

Engine auto-approval + daily board fix + edit-form parity. XP killed. Vault kid/adult/couples-rail. Quest audience filter. Over-broad anon EXECUTE grant revoked. Docs migrated; `north-star.md` created.

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Rename confirmed still present on the live member editor 2026-07-18 — queued for the Haiku sweep.*
- **2026-06-29 → 06-26** — Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, monthly recurrence + audience, Campaigns, Calendar, Briefing, the Vault, PIN, Quest Log.

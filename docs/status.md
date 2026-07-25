# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-25 (late)** — *a pure documentation session with zero code delta, which closed the oldest debt on the board and killed a structure that was one day old.* Started as "split the layers into their own governance" and ended by **deleting three of the four layers**. **Net code delta: none. Net Lovable credits: none. Net doc delta: `master-spec.md` fully regenerated (the four-item fold, finally), ten `decisions.md` entries, both live docs replaced.**

**The structural finding, and it superseded a decision made the same day.** The 07-25 (early) call that the four avatar registers are four functional layers **failed decomposition**: Keep (upkeep, chores, repair, DIY) is quests. Garden (yard, planting, outdoor maintenance) is quests. Hall (celebration, hosting, parties, feasting) is lists, campaigns, and calendar events. All three are content and theme on machinery that already exists — which is the definition of a **register**, not a layer. **Only training failed to decompose**, and for a structural reason: it cannot mint embers without breaking the second-party rule, because nobody approves a squat set. **Emberhold is a one-module product with one optional module candidate, and the four registers went back to being what they always were — an avatar class and an ambient theme.**

**Two constitutional rules were rewritten, both because they were household-module rules stated as platform rules:**
- **"One currency" → NO CONVERSION.** Plurality was never the danger; an exchange rate was. Recorded as standing law with nothing currently to govern.
- **"Embers mint only on adult approval" → real-world redeemability requires a second party to mint.** The rule follows convertibility, not effort. Sealed in-app currencies need no second party; a self-logged set only cheats the squat.

**Endure was read and scoped out.** Six locked/draft docs from `endure-canonical` reviewed. **The blocker is structural: the fitness bridge requires Apple Health / Google Fit with heart rate as gatekeeper, there is no HealthKit web API, and the PWA posture is LOCKED (Capacitor DECLINED 07-22).** Self-logged strength is the only modality a PWA can honestly measure — which makes the entire hybrid layer (the largest locked doc in that repo) dead on arrival here. **Principles ported, systems did not.** Endure is not killed; it was already blocked on being native and remains so.

Last session (prior): **2026-07-25 (early)** — *a greenfield product session that ended by folding a whole second app back into Emberhold, and left one real security finding behind it.* **Net code delta: one column, one RPC, one unrouted screen, one tested pure-TS library.** An entire parallel tenancy (`holds` / `hold_accounts` / `members` + five training tables) was built inside the production project and removed the same night, empty and never reachable from navigation. **The accidental find that matters most: `anon` held full CRUD on `families`, including UPDATE on `is_founder`** — the column LOCKED service-role-only on 07-19. Revoked. **Grant-drift instance six, and it re-prices the table-grant read to front-of-queue.**

Last session (prior): **2026-07-23** — *the household-local date seam.* The daily-quest bug root-caused to `roll_missed_dailies()` firing on every board mount and judging fresh rows against server UTC. **A full grep found six live violations across two functions, not one.** Fixed as `families.timezone` + `household_today()`. **Create-path leg verified on the glass; two legs still open.**

Last session (prior): **2026-07-21 (late)** — *the SECURITY DEFINER grant surface.* Postgres grants `EXECUTE` to `PUBLIC` by default on every new function; seven were carrying it. Fixed as one grant-then-revoke migration, re-verified clean.

Last session (prior): **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit RUN, breached, fixed, re-verified.* **The isolation model is verified sound.** Two grant-drift breaches closed. **Security distribution gate cleared**, with a grant-enumeration asterisk — closed for functions, **still open for tables and now urgent.**

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate, a tenant-isolation model verified sound under live authenticated attack, a function-grant surface that is provably clean, a quest date model deriving household-local time from one named server-side function, a household-level module-enablement column with no consumer yet — and, as of tonight, a canonical spec that finally describes all of it.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**Emberhold is a ONE-module product with ONE optional module candidate.** The four registers (Forge / Garden / Keep / Hall) are **aesthetic only** — an avatar class and an ambient theme, gating nothing. **Training is the sole module**, it is not built, and **its shape is an open decision** (Option A: tool only. Option B: tool plus one sealed game. **A ships before B is designed.**).

> **`families.enabled_modules` is correctly named as shipped and needs no migration.** `['household']` = base; `['household','training']` = base + training. An earlier recommendation to rename it to `enabled_layers` was predicated on there being four layers and evaporated with them. **Do not act on it.**

> **The remaining structural gap is still activation, not security.** Onboarding ends at setup, not at activation: a new hold lands on a board with no doorway to the first quest. That same build owns module intent and timezone capture. See CRITICAL PATH #2.

> **The table-grant surface has not been re-read since the 07-16 outage, and 07-25 proved it is not clean.**

See `north-star.md` for the gate ladder. **Note: north-star has not been updated for the module architecture or the constitution restructure — see OPEN.**

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **✅ P4×L8 tenant-isolation audit — SECURITY GATE CLEARED (2026-07-19).** Function-grant residual CLOSED 07-21 late. | — |
| **1a** | **🔴 TABLE-GRANT READ — THE TOP SECURITY ITEM.** One accidental sample found `anon` with full CRUD on `families` including `is_founder`. Tables unread since the 07-16 outage — which was the *opposite* failure (grants too narrow). **Read every table's grants for `anon` and `authenticated`; revoke anything unearned.** | Gate B honesty. |
| **2** | **Pip first-run onboarding + empty-board doorway + module intent + timezone capture.** **The top structural blocker. Four payloads, one screen.** | Gate D. Activation. |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Off the critical path but the best ROI on the board: the training module, Option A.** Replacing Fitbod at $15.99/mo is **~$192/yr against a $636/yr break-even target** — roughly a third of break-even recovered with no paywall, no funnel, and no Gate E dependency. It blocks nothing and unblocks money.

**Still owed and cheap:** the **Clean Toys discriminator** (30 seconds), the **overnight rollover check** (next morning board load), and the **Monday reappearance test** (07-27 — just look).

---

## 🟢 SHIPPED / RESOLVED — 2026-07-25 (late): the constitution restructure

**Zero code. Three documents.**

- ✅ **`master-spec.md` FULLY REGENERATED — the oldest debt on the board is closed.** All four owed folds landed: the **recurring lifecycle** (successor anchoring + the `roll_missed_dailies`-fires-on-every-mount behavior, previously undocumented), the **activeness model** (`isActiveQuest` as the single shared predicate), the **household clock** (its own section, `CURRENT_DATE` banned in any `due_date` expression), and the **module architecture**. Restructured into **Part I — the constitution** (seven supreme rules) and **Part II — the training module, shape undecided.**
- ✅ **Five stale status leaks stripped from the spec.** It said 44 avatars (48), roster transport pending (shipped 07-18), "24 of 40 / free 16" (doesn't reconcile with 32/16 of 48), wall "designed, not built" (built 07-17), and canonical repo "parked, warming" (you're reading this out of it). **The spec's own header said it does not track status. It was tracking status in five places, all stale.**
- ✅ **Ten `decisions.md` entries appended**, plus a find/replace converting eight `proposed LOCKED` → `LOCKED`.
- ✅ **Vocabulary settled.** `HOLD` (tenant) · `SURFACE` (platform screen) · `MODULE` (optional functional area) · `REGISTER` (Forge/Garden/Keep/Hall, aesthetic only). **"Layer" retired** — coined for four things, described one.

**What was decided about training, and what deliberately was not:**
- **Settled:** module not app · engine is pure client-side TypeScript, never Postgres (a garage has bad wifi) · the reason string is the differentiator, not the math · v1 job is Fitbod replacement (set logging, automatic programming, rest timing, progressive adjustment) · **free/paid split — deterministic and template-driven features are FREE, only LLM-generated programming is PAID** · participation is a per-member flag, not a role check · client-facing training out of scope.
- **NOT settled:** whether it carries a game at all. **Option B (materials → artifacts → collection) is deferred, not rejected, and does not get designed until Option A has been used for a month.** The 07-25 (early) collapse was exactly this shape — a tool wall and a metal system built while still unable to log a working set.

**The Endure read — six docs, principles only.**
- **Ported:** never decays / absence never punished / no failure states (**which Emberhold already does independently** via no-guilt-pile daily rollover — the same instinct arrived at twice) · recognition not reward · "destroying loot is not failure, it is authorship" · **expression vs. marks** — expression is choice (free, reversible), a mark is memory (earned; what's paid for is the ritual of permanence). **That last one is folded into the spec as a sharper catalog test than "delight vs. function": is this thing a CHOICE or a MEMORY?**
- **Not ported:** loot tables, blueprints, ten identity pools, clans, five construction phases, drift ceremonies, aspect states.
- **A contradiction worth knowing about:** `resources-currency-system.md` LOCKS *"a single unified currency; no modality produces a more valuable or more useful currency than another — non-negotiable."* The materials-by-modality idea is the opposite call from the same person. Moot under Option A; **it is one of the things Option B owes.**

**New working-model principle: DECOMPOSE BEFORE YOU PROMOTE.** Three of four proposed functional areas dissolved into existing machinery under inspection — in the same session they were proposed, and after a full spec regeneration had already been written around them. Name the thing, then list what it would actually be built from. **If the answer is entirely existing surfaces, it is content or theme, not architecture.**

---

## 🟡 PENDING VERIFY

- 🟡 **Daily rollover across midnight (07-23) — THE LEG THAT MATTERS. STILL OPEN.** Both the filter and the write in `roll_missed_dailies` changed. **The failure mode is not a wrong date — it is dailies silently never rolling at all.** First board load of the morning: confirm yesterday's daily is gone and a fresh one is open and dated today.
- 🟡 **Monday reappearance test (STAGED, 2026-07-27) — carrying two payloads.** Verifies the 07-21 anchor logic **and** `household_today()` in trigger context simultaneously. **If it fails there are two candidates, not one.** *Nothing to do until Monday — just look.*
- 🟡 **Fourth-activeness-surface discriminator — 30 seconds, still not done.** See OPEN.
- 🟡 **`/setup/intent` render check.** Never loaded by a human. **HTTP 200 on `/board` is not a render check for a different route.**
- 🟡 **Grant-revoke verification probe job — BRIEF DRAFTED, DEFERRED BY SCOTT.** Grants confirmed correct *on paper*; nothing has confirmed they didn't break a live path. **One distinction:** `SQLSTATE 42501` = broken grant (FAIL); any application-level error = the call reached the function body (PASS). Probe all six revoked RPCs plus three untouched controls, real authenticated JWT, non-existent UUIDs, no writes. **Add the `families` anon revoke to scope.** *Sonnet · auto-accept OFF · read-only.*
- 🟡 **`handle_quest_approval` anchor migration (07-21) — landed but not independently confirmed on `origin/main`.**
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, **check the wall specifically.**
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit.** Approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — the module layer

- ⬜ **🔴 `enabled_modules` has no consumer.** The column and its RPC exist; nothing reads them, no screen is gated, `/setup/intent` is unlinked. **Deliberate and also debt** — an unread config column gets rediscovered in three months and mistaken for dead code. Either wire it (with onboarding, CRITICAL PATH #2) or delete it. **Do not let it sit unowned.**
- ⬜ **🔴 `north-star.md` is now out of date and it is the doc that says "read this first, cold."** It describes the gate ladder correctly but knows nothing about the constitution restructure, the module architecture, or the two rewritten rules. **It also still owes the success definition and the $636 target**, which jAIne has raised three times without a call. **The blocker is protocol, not content** — north-star is not one of the four close-out deliverables and jAIne will not invent a fifth container. **Two options: (a) add it as a fifth full-replace file at close, or (b) a dedicated pass. Pick one.**
- ⬜ **The training module's shape is an OPEN DECISION** (Option A vs. B). Recorded in `decisions.md` and `master-spec.md` Part II. **A ships first; B does not get designed until A has been used for a month.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** A twelve-year-old lifting in a garage gym is normal. When it breaks it is a per-member flag, not a role check. **Do not build now.**

## ⬜ OPEN — carried

- ⬜ **🔴 POSSIBLE FOURTH ACTIVENESS SURFACE — DISCRIMINATE FIRST, IT'S ONE QUESTION.** "Clean Toys Downstairs" (daily, IN PROGRESS, assigned to Mia) appeared in the Quest Log but **not** in Mia's roster/profile "active quests" list. **Expand it in the Quest Log and read its due date.**
  - **Future-dated → pre-fix debris.** Delete it and move on.
  - **Today or earlier → a fourth surface carrying its own predicate.** The 07-21 unification swept `board.tsx`, `Briefing.tsx`, `wall.tsx`. The member profile/roster view was **not** in that list — which would make the LOCKED activeness decision partially untrue as written.
  - **Why it outranks its size:** the harm is a kid claiming a quest and not seeing it on her own profile — the "where'd my chore go" failure, aimed at the person the app exists to be fun for.
- ⬜ **STALE chip may be a fourth read of `due_date`.** The weekly wears a STALE badge and the dailies don't. Verify, then fold or leave deliberately.
- ⬜ **Household timezone capture at setup — a Gate B item.** `families.timezone` defaults to `America/Los_Angeles`, correct for all 13 current households and **silently wrong for the first stranger in Ohio.** Capture `Intl.DateTimeFormat().resolvedOptions().timeZone` during hold setup. **Belongs with onboarding.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** Logged so it is not rediscovered as an oversight.
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile. Cosmetic; correctly hidden.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission? Decide deliberately.** *(Currently load-bearing as a diagnostic surface — it's how both date bugs were confirmed.)*
- ⬜ **Module navigation is unresolved.** Seven tabs is already one past mobile comfort, and an enabled module needs somewhere to live. **It is not "add another tab."**

---

## 🟢 SECURITY TRIAGE — statuses as of 2026-07-25

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated 07-21 late, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: `confirm()` copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`. *(Re-confirmed 07-25 from a second angle — the anon-revoke preflight independently verified the join path never reads `families` with the anon key.)*
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.** Intra-household, not cross-tenant. **Revisit if the own-session-vs-per-member-auth fork is decided.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** `wall_request_redemption`; the wall proposes and never commits. **Same revisit trigger.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.** Structurally unclearable.

**Fixed:**
- ✅ **Public/anon can execute SECURITY DEFINER (lint 0028) — FIXED & RE-VERIFIED 07-21 late.**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable — FIXED 07-19, re-confirmed 07-21.**
- ✅ **`anon` full CRUD on `families` incl. `is_founder` — FIXED 07-25.** Table + column grants revoked; verified zero. **Directly contradicted a LOCKED rule. Instance six of grant drift.**

**Real, open:**
- ⬜ **🔴 TABLE-GRANT READ — the top security item.** See CRITICAL PATH #1a.
- ⬜ **Activity-log forgery — actor label is client-supplied.** `append_activity` takes `_actor_label text` as a parameter. **The activity log is the receipt surface**: everything else accepted is "a kid could do a thing," while this is "a kid could make the record say an adult did it," corrupting the surface an adult would use to catch the first thing. **Fix shape:** derive from `auth.uid()`.
- ⬜ **Adult PIN plaintext in `localStorage` during signup — CONFIRMED, QUEUED. CREDIT-FREE.** Collect it in post-confirmation finish-setup; don't persist client-side.
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix them together in one audience-read pass**, *with* the own-session-vs-per-member-auth decision.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. Low priority.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-25 (early): the module reframe

- ✅ **`families.enabled_modules text[] NOT NULL DEFAULT ARRAY['household']`** — all 13 households backfilled; behavior unchanged. **Confirmed 07-25 late: correctly named, no migration needed.**
- ✅ **`set_enabled_modules(_modules text[])`** — SECURITY DEFINER, `search_path` pinned, EXECUTE to `authenticated` only, PUBLIC and anon revoked. Adult-gated, validates against the allowed set, writes only the caller's own family row.
- ✅ **No new grant on `families`.** Deliberate and load-bearing: `authenticated` retains column-level UPDATE on `name` **only**. The RPC exists specifically so the paywall column never had to become writable to add a feature column.
- ✅ **`/setup/intent`** — unrouted, unlinked, reachable by direct URL only. **Nothing reads `enabled_modules` yet.**
- ✅ **`src/lib/progression.ts` + `progression.test.ts`** — RPE-driven autoregulation, plate-snapping, Epley+RIR e1RM. **10/10 tests, zero Supabase imports, zero network.** Retained through the teardown; **the only artifact of that night that survived, and it survived because it had no Supabase imports.**

**Built and removed the same night:** `holds`, `hold_accounts`, `members`, plus `exercises`, `equipment_inventory`, `prescriptions`, `training_sessions`, `set_logs`, and the functions `current_hold_ids()` and `provision_forge()`. **All eight tables held zero rows except two throwaways. Neither new route was reachable from navigation; `/board` never degraded.**

**Two failure modes, both worth keeping:**
1. **jAIne wrote two full Lovable prompts and a complete schema without fetching a single canonical doc.** The invented noun `holds` — a *third* name for a tenant already called `families` — is what the protocol would have caught in thirty seconds.
2. **jAIne then ratified Lovable's unbriefed scope**, treating an explicit DO-NOT-BUILD violation as intent without knowing what tables were underneath. **The prompt's negative constraints were correct and were simply not enforced by anyone.**

**Recon before teardown**, with a material caveat: Postgres records no DDL timestamps, so recency inferred from OID ordering **over-reported** — four functions flagged as "created today" were documented 07-19/07-23 objects. **An OID heuristic dates objects relative to each other, never absolutely.**

**Revert considered and rejected on evidence.** Lovable's version history restores code and project files only — it does **not** roll back applied migrations, and it **removes migration files while leaving their objects live**. Given schema-history drift is already a named landmine, a revert would have manufactured a new instance to clean up an old one. **Forward migration instead.**

---

## 🟢 SHIPPED / RESOLVED — 2026-07-23: the household-local date seam

**The symptom.** A newly created daily did not appear on the board; the Quest Log showed it dated tomorrow.

**The wrong hypothesis, named because the process caught it.** jAIne's confident opening call was that the create path branched on recurrence. It does not. **The recon brief was written to force a ruling between two named alternatives rather than to confirm the hypothesis. That framing is why one pass found the real mechanism.**

**The real mechanism.** `board.tsx` fires `roll_missed_dailies` unconditionally on every mount; `create.tsx` navigates to `/board` on success. The RPC ran seconds after creation, judged the fresh row against server UTC, archived it, and respawned it dated tomorrow.

**The enumeration.** A full grep of `supabase/` found **six live violations across two functions** — `roll_missed_dailies()` (2 sites) and `handle_quest_approval()` (4 sites). Frontend: zero.

**What shipped.** `families.timezone` (IANA, NOT NULL), `household_today(fam uuid)` STABLE, all six sites replaced, both signatures unchanged, zero frontend changes. **The fix shape changed mid-session because `handle_quest_approval` is a trigger** — no caller, no place to pass a client-supplied date — which structurally killed the client-stamp approach and forced the right answer.

**Verified.** Catalog re-read: no `CURRENT_DATE` in any expression touching `due_date`. **Glass: a daily created post-migration appears on the board and in the member roster immediately.**

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (late): the SECURITY DEFINER grant surface

**✅ Row 0029 — PERMANENTLY IGNORED.** A definer RPC callable by authenticated users *is* the architecture.

**✅ Row 0028 — REAL, ROOT-CAUSED, FIXED.** **Seven functions carried `PUBLIC` + `anon`.** **Root cause:** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly-created function. The affected set was chronological, not random — `enforce_quest_family_refs` was created **inside the 07-19 grant-drift fix bundle itself**. **Every revoke this project has ever done was a reactive one-off against a mechanism that re-breaks the default automatically.** One migration, **grant-then-revoke in that order**, re-verified by full catalog re-read.

**✅ The 07-19 founder revoke held. ✅ The ignored-issues list validated — five for five**, but for *three different reasons*, and two only because the shared-session model is what it is.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-21 (evening): recurring-quest visibility

**Layer 1 — three definitions of "active," none shared. ✅ FIXED.** `isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts`, imported by `board.tsx`, `Briefing.tsx`, `wall.tsx` (`42fcddb`). *(Possibly not a complete sweep — see OPEN.)*
**Layer 2 — successor anchored to the wrong date. ✅ FIXED (migration).**
**Layer 3 — pre-migration ghost. ⬜ COSMETIC, OPEN.**
**Layer 4 — THE ACTUAL BUG: a timezone skew. ✅ FIXED, VERIFIED ON GLASS.**
**One phantom killed** — the "favorite-chip auto-assigns" bug does not exist. Logged DECLINED.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Verdict-level record only — attack specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound:** cross-tenant reads and writes; `current_family_id()` integrity; every household-scoped definer RPC; the `family_xp` view; the insert trigger; `system_flags` write.
**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` family-scoped; `enforce_quest_family_refs` trigger added.
**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` revoked from `authenticated`. *(07-25: the `anon` half of the same column was still open until that night.)*
**Residual → Scott's dashboard cascade:** the two throwaway families (Alpha/Bravo) + their 3 auth users.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning) / 07-18

**Wall display-mode fine-tune — verified on the physical glass.** Avatars enlarged (~18% of tile width). Quest-count line wrap killed.

**Avatar transport — shipped end-to-end.** 48-roster into `src/assets/avatars/`, committed, synced. Tier-tagged free/founder (16/32). Global gate as a DB value (`system_flags.founder_gate_enabled`, seeded false) — **flip is a one-line UPDATE, not a build.** Re-forge: 28/28 invalidated, run live.

**Doc-protocol repair + catch-up committed.** Sync-to-origin rule folded into `session-protocol`.

- ⬜ **Re-forge reach across the 13 (real defect, later fix).** Only the member who logs in is prompted; others render the fallback placeholder until an adult manually fixes each.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." Verify and align.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **🔴 TABLE-grant read — see CRITICAL PATH #1a.** Function grants provably clean as of 07-21 late; **tables unread since the 07-16 outage**, where fourteen tables lost their `authenticated` Data-API grants — and 07-25 found the opposite failure (anon grants too *wide*) on the one table it happened to look at. **Same disease, both directions.** ⚠️ *Findings detail stays in the Code/Workstream session.*
- ⬜ **Grant-revoke verification probe job** — see PENDING VERIFY.
- ⬜ **Activity-log actor forgery** — derive actor server-side from `auth.uid()`.
- ⬜ **`sandbox_exec` — unexplained role holding EXECUTE on every function in `public`.** Pre-existing, so **not drift** — almost certainly a Lovable platform role. **Ask Lovable what it is.** Recon, not alarm.
- ⬜ **PIN-plaintext-in-localStorage** — credit-free Code frontend commit.
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Household timezone capture at setup** — Gate B; belongs with onboarding.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies", the 07-21 test weeklies, the ghost "Take out the trash" successor, and **the 07-22/23 pre-fix test dailies — these carry future `due_date` values and will keep presenting as a failed fix every time this area is tested.** **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding + empty-board doorway + module intent + timezone.** **The top structural blocker.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.
- ⬜ **`north-star.md` catch-up** — see OPEN. It is the doc that says "read this first, cold," and it no longer describes the product.

## ⬜ OUTSTANDING — polish

⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint** · **"Forgot PIN" confirm() copy** · **STALE chip predicate**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The training module's Option-B game (materials → artifacts → collection)** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Re-forge reach across the 13** · **Own-session vs per-member-auth** (rewards/quests audience reads wait on it — *and two ignored scanner findings revive if it's decided*) · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED 07-22) · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19, re-affirmed 07-21)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Two scanner findings are ignored *because of* this boundary**; deciding the own-session-vs-per-member-auth fork revives both. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **DECOMPOSE BEFORE YOU PROMOTE. (NEW 07-25 late.)** Name a proposed structure, then list what it would actually be built from. **If the answer is entirely existing surfaces, it is content or theme, not architecture.** Three of four proposed functional areas dissolved under this check — after a full spec had already been written around them.
- **Fetch the canon before producing anything. (07-25 early — the hardest-won line of that session.)** jAIne wrote a full schema and two build prompts from memory and invented a third name for a tenant that already had one. **The protocol is not ceremony; it is what catches an invented noun in thirty seconds instead of eight tables later.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.** Read what landed against what was briefed, every time — and never ratify unbriefed scope without reading the schema underneath it.
- **An OID heuristic dates objects relative to each other, never absolutely.**
- **A code revert is not a database revert.** Restores files only, removes migration files while leaving their objects live. **Undo schema forward, always.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first.
- **Hand-applied DB changes are forbidden — everything lands as a migration file.** Live-schema-drift is a 6×-observed pattern.
- **A Code job isn't done until the artifact is observable from outside the agent.** **Verify `origin/main`, not the agent's report.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X.
- **Sweep the class before fixing the instance.** Seven functions carrying a grant default (07-21); six date sites across two functions (07-23); **and the one table anybody happened to read had wide-open anon grants (07-25) — which is an argument about the twenty that weren't read.**
- **Enumerate before you triage.**
- **A defensive layer is not verified by the migration that adds it.** Fix → re-read the catalog → confirm on the live artifact. **And a catalog read is not a glass test** — it proves the code is right, not that the behavior is. **HTTP 200 is not "renders," either.**
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies. *(Re-confirmed 07-19 twice; join path independently re-verified 07-25.)*

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

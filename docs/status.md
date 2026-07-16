# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-15** — *the admit-on-approval verification audit finally ran — live against the database — and it earned its keep.* The join-code bypass (#1) is confirmed **genuinely closed**. But the audit surfaced a **separate, live privilege-escalation** the 07-14 migration shipped: `complete_household_setup`'s reconcile branch trusted a caller-supplied `_role`, letting an active kid mint themselves a parent `user_roles` row. **Found and fixed 07-15**, verified by re-reading the branch. The admit-on-approval **frontend brief is fired** to Claude Code — build in flight, **not yet verified**. Method that caught it: Lovable extracts the live catalog (`pg_policies` / `pg_proc`), jAIne judges independently — the author does not grade its own exam.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — the authorization-layer findings are being worked, not open-ended.** As of 2026-07-15: findings #5 and #8 were already patched by a June migration; #6 (kid self-approval / ember self-minting) re-audited and CLOSED (blocked by the `a_enforce_quest_update_authority` trigger); #1 (join-code bypass, CRITICAL) closed at the data layer 07-14 and **verification-audited 07-15 against the live DB — confirmed genuinely closed.** That same audit found and fixed a **separate live escalation** in `complete_household_setup` (see below). Remaining: the admit-on-approval **frontend** is in flight (Claude Code), and the **P4×L8 tenant-isolation audit** (the mortal-peril item) is still outstanding. See the critical path.

> **Onboarding ends at setup, not at activation.** A new household lands on a board that says "all quiet" — and, per 07-14, says it *over a live board with open bounties and a fresh completion*. The empty-board doorway fix is doubly earned.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **Admit-on-approval — verification DONE, frontend IN FLIGHT.** Live-DB audit ran 07-15: join-code bypass confirmed closed, one separate escalation found + fixed. Admit UI + pending/denied routing briefed to Claude Code — **build in flight, unverified.** Visual verifies (queue renders, kid-default selected) are Scott's lane, not Code's. | Distribution. Gate B. |
| **2** | **P4×L8 — the tenant-isolation audit (Workstream 1)** | Distribution. The mortal-peril item. Hold Alpha exists. |
| **3** | **Avatar transport** — *generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain.* **Tabled by Scott** — on the board, not a priority; do not push it, note it. | The Guildhall, the delight layer |
| **4** | **Pip first-run onboarding screens** | Gate D. Activation. Day-8 retention. |
| **5** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen people have already received it. |
| **6** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

---

## 🔴 ADMIT-ON-APPROVAL — data layer VERIFIED, escalation FIXED, frontend IN FLIGHT

**Data layer (shipped 2026-07-14, Lovable, migration reviewed before commit):** enum `profile_status` = `('active','pending')`; `profiles.status` default `active` is the gate; `current_family_id()` returns **NULL** for any non-active profile, so every family-scoped RLS policy denies a pending member by construction — one fail-closed chokepoint. Join-by-code creates a **pending** profile with `role='kid'` as an inert placeholder and the self-selected role stored as advisory `requested_role`; **no `user_roles` row is written at join.** `enforce_profile_role_change` blocks non-parents from writing `status`/`role` at the trigger layer.

**The RPC contract (what the frontend builds against):**

| RPC | Who | Returns / Effect |
|---|---|---|
| `get_pending_membership()` | pending self | `(profile_id, family_id, family_name, requested_role, requested_at)` — for the "waiting for admission" screen |
| `list_pending_members()` | parent-only | `(profile_id, name, avatar_emoji, requested_role, requested_at)` |
| `admit_pending_member(_profile_id uuid, _confirmed_role app_role)` | parent-only | flips `status→active`, sets `role = _confirmed_role` (**confirmed role authoritative — request is advisory**), clears `requested_role`, inserts `user_roles`, logs `member_admitted` |
| `deny_pending_member(_profile_id uuid)` | parent-only | logs `member_denied`, **deletes the pending profile row** so the auth user can retry |

**✅ VERIFICATION AUDIT — RAN 2026-07-15, against the live database.** Method: Lovable dumped the live catalog (`pg_proc`, `pg_policies`, trigger defs, grants) read-only; jAIne judged independently in-context. Result per claim:
- ✅ **NULL-denial total.** Pending → `current_family_id()` NULL → all 47 policies deny. The two `= auth.uid()` self-branches (`profiles` self-UPDATE, `user_roles` self-SELECT) are harmless, fenced by the trigger.
- ✅ **Trigger coverage.** `enforce_profile_role_change` guards role/status/family on `profiles`. (`rls_forced = f` on every table is expected — forcing only affects the *owner* role, not `authenticated`. Not a finding.)
- ✅ **SECURITY DEFINER self-checks.** All four pending RPCs — and the wider surface (redemptions, PINs, adult-profile creation, email lookup) — check parent role + family scope internally. Cross-household admit blocked cold.
- ✅ **Deny half-state (DB side).** Denied user = authenticated, no profile, no pending — identical to a fresh pre-join signup. Clean at the DB; becomes a hard **frontend** requirement (route to join/create, not a dead screen).

**🔴→✅ ESCALATION FOUND + FIXED 2026-07-15 (the audit's real catch):** `complete_household_setup`'s "already has a profile" reconcile branch inserted a `user_roles` row from the **caller-supplied `_role`** with no check against the profile's real role. An active kid could call the RPC with `_role='parent'` and mint themselves a parent role; `has_role()` reads `user_roles`, so that one forged row made them a parent everywhere while their profile still read "kid." Step-1 grant check confirmed it was **live and reachable** — `EXECUTE` was held by `anon, authenticated, service_role, sandbox_exec, postgres`. **Fix (Lovable, reviewed):** the reconcile branch now derives the role from `profiles.role` (trigger-protected), never from `_role`. Create and join branches untouched; signature unchanged. Verified by re-reading the branch, not by "migration succeeded."

**🟡 STILL OPEN — the admit-on-approval frontend (briefed to Claude Code, in flight):**
- 🟡 **Admit UI.** Reuse the `ZonePending` pattern in `Briefing.tsx`. Parent-gated queue over `list_pending_members()`; Admit opens a role confirmation (**defaults to KID**, requested role shown as context only, promotion is a deliberate tap) → `admit_pending_member`; Deny → `deny_pending_member`, refetch.
- 🟡 **Waiting + denied routing.** Pending user sees a themed "waiting for admission" screen via `get_pending_membership()`. **The deny guard:** authed + no profile + no pending must fall through to join/create, not a dead screen or spinner.
- **Verify posture:** Code can confirm the RPCs are wired and the routing logic branches correctly by reading. The four visual verifies (queue renders, kid-default selected, deny clears the row, waiting screen shows) are **Scott's lane** — "shipped" means an actual pending member run through admit *and* deny on a phone, not a green Code report.

---

## 🟢 SHIPPED — 2026-07-15

**Admit-on-approval verification audit — ran live, join-code bypass confirmed closed**
- ✅ Live-DB extract-and-judge audit executed: Lovable dumped `pg_proc`/`pg_policies`/triggers/grants read-only; jAIne judged independently. Finding #1 (join-code bypass) confirmed **genuinely closed** — join branch writes pending + advisory `requested_role` + no `user_roles` row; all four pending RPCs self-check parent + family scope.

**Live privilege-escalation in `complete_household_setup` — found and fixed**
- ✅ Reconcile branch trusted a caller-supplied `_role` → active kid could mint a parent `user_roles` row (escalation via `has_role`, which reads `user_roles` not `profiles.role`). Live + reachable: `EXECUTE` granted to `anon, authenticated, service_role, sandbox_exec, postgres`.
- ✅ Fixed: reconcile now sources role from the trigger-protected `profiles.role` column. Create/join branches and signature unchanged; verified by re-reading the function body.
- **Scar, on the record:** this hole was **shipped live by the 07-14 migration** — the one that was reviewed before commit. Review-before-commit is not verification. The live-DB audit is what caught it. See `decisions.md`.

---

## 🟡 PENDING VERIFY

- 🟡 **Admit-on-approval frontend** (above) — briefed to Claude Code, in flight. Visual verifies are Scott's.
- 🟡 **Recurrence chip legibility.** Anchors are invisible to users — a kid sees "MONTHLY" with no hint it means the 1st. `RECURRENCE_LABEL` enrichment ("Weekly · Mon" / "Monthly · 1st") was scoped and **skipped** (more than a one-line change). Parking-lot follow-up.
- 🟡 **Lists collapsible sections** — landed (`fb6aa99`), rendered. Not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven on fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. Never confirmed.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.

---

## 🔴 THREE BOARD BUGS SURFACED 2026-07-14 (captured, not yet fixed)

- ⬜ **The empty-board eulogy is a lie on a live board.** "All quiet at the hold" renders directly above "4 open" and a fresh 2h-old completion. Says quiet while showing activity. This is the doorway-not-eulogy fix, now with evidence.
- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …" in the feed; the 7/12 vocabulary bundle standardized on "QUEST POSTED." Possibly a surface that bundle didn't reach. Verify and align.
- 🔵 **Campaigns are live in the wild.** A "São Paulo Trip" campaign tag is visible on a real quest card — campaigns are in real use, not just built. (Noted, not a bug.)

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit.** Opus, auto-accept OFF. Hold Alpha exists — run it as an attack. **Free input now available:** the 07-15 live-catalog dump + the 25 pre-existing Supabase linter warnings (`SECURITY DEFINER` search_path, function exposure, RLS edges) — hand both to the audit; let it sort real from noise rather than triaging them cold.
- ⬜ **Auth email branding + deliverability.** Sender is `no-reply@auth.lovable.cloud`; display name is **`Family-Quest-Board`** — scaffolding, a name that exists nowhere in the product. Lands in spam. Templates editable in Lovable Cloud → Emails. Custom sender: DNS at Porkbun (free) + SMTP (Resend free tier). ⚠️ *Inspect any NS-record request before pasting — delegation ≠ adding a TXT record.*
- ⬜ **Service worker + app-shell cache** — no functional SW. Makes "installable PWA" true instead of aspirational.
- ⬜ **Backup posture.** Backend is Lovable Cloud; backups/PITR/export/exit are all Lovable's to grant. Code has a Git backup; **the data has none.** Gate B blocker.
- ⬜ **Orphaned auth users** — `scottydawg@gmail.com`, `scott.draper83+alpha@gmail.com`. Delete.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport.** **Tabled by Scott** — a GIMP slog, on the board, not a priority. Full scope preserved in prior status + parking-lot: hand-cut roster, file drop (Haiku), rewrite `HERO_AVATARS`, land `Feast→Hall`, retire `avatar-review.tsx`, invalidate stale `hero:` picks (the re-forge migration), re-lock the free/paid split (the "16/24" arithmetic predates the 48-roster). Do not push; surface when Scott calls it.
- ⬜ **Pip first-run onboarding screens** (see `decisions.md`) + the empty-board doorway fix.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Test-object cleanup** — `Testi`, `testy`, `Daily test`, `iopuyiouh`. Data, not code.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Quick Add default EXPANDED on an empty board.**
- ⬜ **`TITLE` → `QUEST TITLE`** on the create form.
- ⬜ **Lists — "5 OPEN · 348 DONE."** A fossil layer. Absurd to a stranger.
- ⬜ **Pip help discoverability** · **Pip install tutorial** (platform-detected Add-to-Home-Screen card).
- ⬜ **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **List suggestions in onboarding**.

---

## 🅿️ PARKED

See `parking-lot.md`. Display/wall mode · #8b admin-reporting surface · kid-vs-kid impersonation · kid-auth (declined) · Ranks as a household dial · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · recurrence miss-asymmetry · recurrence chip legibility.

---

## 🔵 THE BUILD MODEL — holding

**Claude Code frontend build proven and repeating.** Refuses to guess, stops at scope edges, extends existing helpers rather than authoring parallel ones. That's the behavior we keep wanting; it's now consistent.

- **Code's lane is TEXT** — anything verifiable by reading. The moment the criterion is "does this look right," it's outside the lane.
- **New this session — the live-DB audit method.** For data-layer security verification: Lovable (which owns Lovable Cloud) extracts the live catalog read-only; an independent agent judges. Solves both "local tools can't reach Lovable Cloud's DB" and "the author shouldn't grade its own exam." See `decisions.md`.
- **Model routing:** Haiku for recon/greps/mechanical edits · Sonnet for build jobs with latitude · **Opus for the tenant-isolation audit only.**
- **One writer at a time.**
- **Lane split for migrations:** data-layer changes go through **Lovable** (owns the DB, proven path); frontend goes through **Code**.
- **Still unproven:** whether Lovable *runs* an externally-pushed migration. The avatar `hero:` reset remains the cheap first test — and it's tabled.

---

## ✅ SHIPPED — 2026-07-14

**Admit-on-approval data layer** — enum + pending gate + fail-closed `current_family_id()` chokepoint + join-branch pending profile + four RPCs + trigger-layer self-promotion block. All 23 members backfilled to active. *(Verification-audited 07-15 — see above; the audit also caught the reconcile-branch escalation.)*

**The hub open-bounty count now tells the truth**
- ✅ Hub showed "8 open," board/kid profiles showed 4. Cause: the board gates on `due_date <= today` (correctly hiding future-dated recurring instances); the Briefing omitted the gate.
- ✅ Fixed by routing all three surfaces through one shared predicate: `isOpenBountyVisible(quest, role, today = todayIsoDate())` in `src/lib/quest-helpers.ts`. Verified in pixels: hub, board, kid drill-in all read 4.

**Recurrence reworked to fixed calendar anchors**
- ✅ `handle_quest_approval` rewritten: weekly = Monday of the following week; monthly = the 1st; daily = tomorrow. `recurrence_day` removed end-to-end (trigger, both INSERT lists, create form, edit sheet, column dropped). Grep clean.
- ✅ Live re-anchor: 8 rows before / 8 after, 7 re-anchored, 0 dupes, 0 orphans. Past-due monthlies left in place.
- **Reason it mattered:** relative recurrence *drifts* — a late-approved quest walks its own due date forward. That's the human-admin-dependent rot Emberhold exists to beat, living in the recurrence engine.

---

## ✅ SHIPPED — 2026-07-12 (day)

**Claude Code proven as a build lane**
- ✅ Feed vocabulary bundle landed live (`c9069a6`) — Dim/Warm/Hot/Blazing ramp from `ember_delta`; `BOUNTY POSTED` → `QUEST POSTED`. Code promoted a private `feedHeat()` to a shared helper rather than duplicating.
- ✅ `vault.tsx` TIER_LABEL dedup (`7132605`) · Lists collapsible sections (`fb6aa99`).

**Auth**
- ✅ Email confirmation required; holding-state signup flow live; password reset verified; min length 6→8; re-auth on password change ON.

**Hold Alpha — the instrument**
- ✅ A second isolated household (`Ember Better Self` / Tucker + Dale) with its own owner, roster, quests, reward, approved completion, minted embers, and `activity_log` rows. P4×L8 can be run as an attack.

**The real user base**
- 🔵 13 accounts exist, not four. In the app for two weeks, signed in as recently as 3 days ago.

---

## ✅ SHIPPED — 2026-07-11

Engine — daily respawn verified; quest lifecycle verified end-to-end. Vocabulary — `OPEN BOUNTY` chip killed via grep audit; `XP` eliminated from install prompt. Render — safe-area inset fix. Avatars — roster regenerated: 48 characters, 19 sheets, ~600px each; playbook rewritten. Claude Code stood up (native Windows); **Lovable ↔ GitHub sync connected** — the first backup the codebase ever had.

---

## ✅ SHIPPED — 2026-07-10

Engine — adult quest auto-approval · daily recurring board fix · edit-quest form parity. Copy — XP killed as user-facing vocabulary. Vault kid view — dual-mode catalog/curated. Vault adult view — three zones. Couples reward rail (`reward.audience`) — first schema change of the run. Quest audience filter + badges + copy. Security — over-broad anon EXECUTE grant on a data-mutating RPC revoked. Docs — master spec current; `north-star.md` created; migrated to this repo.

---

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Rename never landed in code — found 7/12, still outstanding via avatar transport.*
- **2026-06-29 (eve)** — Lists v1, shopping-list import, invite share + deep link, notification bell, PIN/password recovery, collapsible avatar picker, Avatar Style Spec.
- **2026-06-29 (AM)** — Activity feed spine, feed on the Briefing, monthly recurrence + audience flag, Parent/Family → Adult/Hold sweep.
- **2026-06-28** — Brand marks, Open Bounties strip, multi-day events, FAB-becomes-submit, avatar roster v1, Pip onboarding, Combat→Forge.
- **2026-06-27** — Campaigns, Calendar, the Briefing, member identity colors, login rewrite, juice pass 1.
- **2026-06-26** — The Vault, starter quest library, fail-closed PIN, Parent Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks, profile, quest detail, `+` FAB, brand.

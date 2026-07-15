# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-14** — *a foundation-deepening session, and three things actually shipped.* Admit-on-approval closed at the **data layer** (pending-member gate, fail-closed chokepoint, admit/deny RPCs) · the hub/board open-bounty **count mismatch** fixed and de-duplicated to one shared predicate · **recurrence reworked** from relative dates to fixed calendar anchors (weekly=Monday, monthly=the 1st), `recurrence_day` removed end-to-end. Two loose ends left deliberately on the admit-on-approval work — see the critical path.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — the authorization-layer findings are being worked, not open-ended.** As of 2026-07-13: findings #5 and #8 were already patched by a June migration; #6 (kid self-approval / ember self-minting) re-audited and CLOSED (blocked by the `a_enforce_quest_update_authority` trigger); #1 (join-code bypass, CRITICAL) decided as admit-on-approval and now **closed at the data layer** (2026-07-14). Remaining: the admit-on-approval **frontend** does not exist yet, and the **pending-member verification audit has not been run**. See the critical path.

> **Onboarding ends at setup, not at activation.** A new household lands on a board that says "all quiet" — and, per this session, says it *over a live board with open bounties and a fresh completion*. The empty-board doorway fix is now doubly earned.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **Admit-on-approval — the two loose ends.** Data layer is in. **(a)** No parent-facing admit UI exists — a parent cannot admit a new member today. **(b)** The verification/attack-sim audit was written and never run. The hole is *clamped, not stitched, and not proven.* | Distribution. Gate B. |
| **2** | **P4×L8 — the tenant-isolation audit (Workstream 1)** | Distribution. The mortal-peril item. Hold Alpha exists. |
| **3** | **Avatar transport** — *generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain.* **Tabled by Scott** — on the board, not a priority; do not push it, note it. | The Guildhall, the delight layer |
| **4** | **Pip first-run onboarding screens** | Gate D. Activation. Day-8 retention. |
| **5** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen people have already received it. |
| **6** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

---

## 🔴 ADMIT-ON-APPROVAL — data layer SHIPPED, two loose ends OPEN

**Shipped 2026-07-14 (Lovable, migration reviewed before commit):**

- New enum `public.profile_status` = `('active','pending')`.
- `profiles.status profile_status NOT NULL DEFAULT 'active'` — the gate. `profiles.requested_role app_role NULL` — the joiner's advisory pick, never a grant.
- **The chokepoint:** `current_family_id()` returns **NULL** for any non-active profile. Every family-scoped RLS policy already routes through this helper, so a pending member is denied on reads and writes **across the whole schema by construction** — one seam, fail-closed everywhere.
- Join-by-code now creates a **pending** profile; **no `user_roles` row is written at join.** The self-selected role is stored as `requested_role` only.
- `enforce_profile_role_change` blocks non-parents from writing `status` or `role` — self-promotion is stopped at the **trigger layer**, not just policy.
- Create-a-household path unchanged: creator lands `active` / `parent` with a `user_roles` row.
- All 23 existing members backfilled to `active`. No one locked out.

**The RPC contract (what the frontend builds against):**

| RPC | Who | Returns / Effect |
|---|---|---|
| `get_pending_membership()` | pending self | `(profile_id, family_id, family_name, requested_role, requested_at)` — for the "waiting for admission" screen |
| `list_pending_members()` | parent-only | `(profile_id, name, avatar_emoji, requested_role, requested_at)` |
| `admit_pending_member(_profile_id uuid, _confirmed_role app_role)` | parent-only | flips `status→active`, sets `role = _confirmed_role` (**confirmed role is authoritative — request is advisory**), clears `requested_role`, inserts `user_roles`, logs `member_admitted` |
| `deny_pending_member(_profile_id uuid)` | parent-only | logs `member_denied`, **deletes the pending profile row** so the auth user can retry |

Activity verbs added: `member_admitted` (notable), `member_denied`.

**🔴 STILL OPEN — the honest status:**

- ⬜ **No admit UI.** The RPCs exist; nothing calls them. A parent cannot admit anyone. The queue pattern to reuse is `ZonePending` in `Briefing.tsx`. **Frontend default must be KID** — show the requested role as context, never pre-select parent, make promotion a deliberate tap (safe-direction bias, applied to permissions).
- ⬜ **Verification audit never ran.** The attack-sim prompt was written this session and set aside when the count-mismatch work pulled focus. Must prove: every family-scoped policy denies on NULL from `current_family_id()`; no GRANT lets a non-parent write `status`/`role` around the trigger; admit/deny hard-check parent role internally (SECURITY DEFINER runs as owner — a missing internal check = anyone can call them); no leftover RPC path writes `status='active'` or a `user_roles` row on the join branch. **"The migration reports success" is the claim we agreed not to trust on a security fix.**
- 🟡 **deny→delete leaves a dangling auth user.** `deny_pending_member` deletes the profile but not `auth.users`. A denied person can re-run join and re-queue (harmless — still gated). Confirm no half-state where they log in, `get_pending_membership()` returns nothing, and the app dumps them somewhere undefined. Check when wiring the waiting screen.

**Recommended next-session order:** audit first, then build the UI. Building a frontend on an unproven security fix is building on a claim.

---

## 🟢 SHIPPED — 2026-07-14

**The hub open-bounty count now tells the truth**
- ✅ Hub showed "8 open," board and kid profiles showed 4. Cause: the board gates on `due_date <= today` (correctly hiding future-dated recurring instances); the Briefing omitted the gate and counted quests nobody could claim.
- ✅ Fixed by routing all three surfaces through **one shared predicate**: `isOpenBountyVisible(quest, role, today = todayIsoDate())` in `src/lib/quest-helpers.ts` — sole source of truth for status + assignment + audience + the date gate. `todayIsoDate()` defines the day-boundary string once. Verified in pixels: hub, board, and kid drill-in all read 4.
- ✅ The board is the honest surface by design — a done or future-dated recurring quest should not appear (it's a "what needs doing" surface, not a "what exists in the DB" surface).

**Recurrence reworked to fixed calendar anchors**
- ✅ `handle_quest_approval` rewritten: weekly next-due = `date_trunc('week', COALESCE(due_date, CURRENT_DATE)) + interval '1 week'` (Postgres week starts **Monday** — anchored by construction); monthly = `date_trunc('month', …) + interval '1 month'` (the **1st**); daily unchanged.
- ✅ `recurrence_day` **removed end-to-end**: trigger reads, both function INSERT lists, the create form field + payload, the quest-log edit sheet, and the column itself (`DROP COLUMN`). Grep for `recurrence_day` in `src/` returns zero (types.ts regenerates). The "day of month" picker (1–31 + "Last day") is gone — anything bespoke is a **calendar** event, not a recurring quest (the membrane).
- ✅ **Live-data re-anchor:** 8 live recurring rows before, 8 after. 7 re-anchored (4 weekly Wed/Fri/Sat → Monday; 1 future monthly 08-09 → 08-01), 0 archived as duplicates, 0 rows lost their live instance. Past-due monthlies **left in place** — undone work stays on the board rather than vanishing to next month.
- ✅ Board eyeballed: WEEKLY/MONTHLY chips clean, one of each title, no doubles.
- **Reason it mattered:** relative recurrence *drifts* — a quest approved late walks its own due date forward, so the due date depended on when an adult happened to approve. That's the human-admin-dependent rot Emberhold exists to beat, and it was living in the recurrence engine.

---

## 🟡 PENDING VERIFY

- 🟡 **Recurrence chip legibility.** Anchors are invisible to users — a kid sees "MONTHLY" with no hint it means the 1st. `RECURRENCE_LABEL` enrichment ("Weekly · Mon" / "Monthly · 1st") was scoped and **skipped** (more than a one-line change). Parking-lot follow-up.
- 🟡 **Lists collapsible sections** — landed (`fb6aa99`), rendered. Not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven on fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. Never confirmed.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.

---

## 🔴 THREE BOARD BUGS SURFACED THIS SESSION (captured, not yet fixed)

- ⬜ **The empty-board eulogy is a lie on a live board.** "All quiet at the hold" renders directly above "4 open" and a fresh 2h-old completion. Says quiet while showing activity. This is the doorway-not-eulogy fix, now with evidence.
- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …" in the feed; the 7/12 vocabulary bundle standardized on "QUEST POSTED." Possibly a surface that bundle didn't reach. Verify and align.
- 🔵 **Campaigns are live in the wild.** A "São Paulo Trip" campaign tag is visible on a real quest card — campaigns are in real use, not just built. (Noted, not a bug.)

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit.** Opus, auto-accept OFF. Hold Alpha exists — run it as an attack.
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

See `parking-lot.md`. Display/wall mode · #8b admin-reporting surface · kid-vs-kid impersonation · kid-auth (declined) · Ranks as a household dial · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · **recurrence miss-asymmetry** (new) · **recurrence chip legibility** (new).

---

## 🔵 THE BUILD MODEL — holding

**Claude Code frontend build proven and repeating.** Three times this session Code refused to guess, stopped at a scope edge instead of overreaching (skipped the label enrichment, flagged it), and extended an existing helper rather than authoring a parallel one. That's the behavior we keep wanting; it's now consistent.

- **Code's lane is TEXT** — anything verifiable by reading. The moment the criterion is "does this look right," it's outside the lane.
- **Model routing:** Haiku for recon/greps/mechanical edits · Sonnet for build jobs with latitude · **Opus for the tenant-isolation audit only.**
- **One writer at a time.** Enforced this session by sequencing (count fix → recon → recurrence migration → frontend cleanup), never overlapping writes.
- **Lane split for migrations:** data-layer changes go through **Lovable** (owns the DB, proven path); frontend goes through **Code**. A CRITICAL security fix is not the guinea pig for the still-unproven external-push migration.
- **Still unproven:** whether Lovable *runs* an externally-pushed migration. The avatar `hero:` reset remains the cheap first test — and it's tabled.

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

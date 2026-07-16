# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-15** — *a long one, and it earned every minute.* Came in to close the admit-on-approval "black hole"; closed that and four live defects behind it. The admit-on-approval **verification audit ran live against the DB** and confirmed the join-code bypass (#1) genuinely closed — then caught a **separate live privilege-escalation** in `complete_household_setup` (caller-supplied `_role` → active kid mints a parent role), fixed. The **admit button shipped** and the admit→kid-default→waiting-screen loop is verified end-to-end. Two more live bugs surfaced by *using the app*: an **admit/deny enum crash** (`'notable'` not in `activity_significance`) and a **`recurrence_day` prod crash** that had quest creation down for real users — both found, fixed, deployed, verified. A **pending-member roster leak** (pending members showing as tappable switch targets) fixed. The through-line, on the record: **three of the defects traced to 07-14 work the log called "shipped."** Every catch after the first came from Scott using the app, not from reasoning at a screenshot.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, **and now the full admit-on-approval join flow** — a complete product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — one gate left before distribution.** As of 2026-07-15: #5 and #8 patched by a June migration; #6 (kid self-approval) CLOSED (blocked by `a_enforce_quest_update_authority`); **#1 (join-code bypass, CRITICAL) fully CLOSED** — data layer, frontend, and live-DB verification all done, plus a separate escalation the audit caught. The remaining gate is the **P4×L8 tenant-isolation audit** (the mortal-peril item). *Caveat carried forward: the 07-14 data layer was verified **secure** by the live audit, but functional bugs (the enum crash) still shipped inside it — verified-secure ≠ verified-functional. Two different passes.*

> **Onboarding ends at setup, not at activation.** A new household lands on a board that says "all quiet" — and, per 07-14, says it *over a live board with open bounties and a fresh completion*. The empty-board doorway fix is doubly earned. Gated behind distribution.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1).** Opus, auto-accept OFF. The mortal-peril item and the last security gate before distribution. Hold Alpha exists — run it as an attack. Free inputs now banked: the 07-15 live-catalog dump + the 25 Supabase linter warnings. **First: confirm the adversarial harness can even reach Lovable Cloud's DB from outside** (see parking-lot open decision) — if not, the audit degrades to policy-reading. | Distribution. Gate B. |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix | Gate D. Activation. Day-8 retention. *Gated behind #1 — it's for strangers, and strangers come after the door locks.* |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen people have already received it. |
| **4** | **Avatar transport** — *generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain.* **Tabled by Scott** — on the board, not a priority; do not push it, note it. | The Guildhall, the delight layer |
| **5** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

**Next-action fork (Scott's call):** the honest sequence is **P4×L8 first** — it's the gate, and tutorial/display/avatars all sit downstream of the distribution it unblocks. **But display/wall mode is the one delight item that does *not* need the gate open** — it's ambient presence for Scott's *own* household, not for strangers — so it's the defensible morale-build to pull forward if a "fun" session is wanted. Tutorial screens genuinely should wait for the audit (they're built for new users who can't arrive until distribution is safe). See parking-lot LATER for display mode's dependencies.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) is fully closed: data layer, frontend, live-DB verification, and one adjacent escalation, all done.

**Data layer (07-14, Lovable):** enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for any non-active profile, so every family-scoped RLS policy denies a pending member by construction — one fail-closed chokepoint. Join creates a **pending** profile, `role='kid'` placeholder, self-selected role stored as advisory `requested_role`, **no `user_roles` row at join**. `enforce_profile_role_change` blocks non-parents from writing `status`/`role`.

**The RPC contract:** `get_pending_membership()` (pending self, waiting screen) · `list_pending_members()` (parent) · `admit_pending_member(_profile_id, _confirmed_role)` (parent — confirmed role authoritative, request advisory) · `deny_pending_member(_profile_id)` (parent — deletes pending profile, auth user survives to retry).

**✅ Verified live (07-15):** NULL-denial total across all 47 policies; trigger coverage sound; all four RPCs self-check parent + family scope; cross-household admit blocked cold. Method: Lovable dumped the live catalog read-only, jAIne judged independently.

**✅ Frontend shipped (`08229d4`):** `ZonePending` in `Briefing.tsx` extended (not parallel-built) — parent-gated queue over `list_pending_members()`, Admit opens a role confirmation that **defaults to KID** (requested role shown as context only), Deny clears the row. Pending user sees a themed "waiting for admission" screen; the denied-user routing (authed + no profile + no pending → join/create) was fixed, including ripping out a dead `ConfirmedView` path that misread denied users as "confirmed on another device."

**✅ Admit loop verified end-to-end by Scott:** admitted a real pending member, kid-default held on an Adult request, waiting screen resolved, roster updated. This is *shipped* — a human ran the loop, not a green build.

---

## 🟢 SHIPPED — 2026-07-15 (the session's haul)

**Live privilege-escalation in `complete_household_setup` — found & fixed**
- ✅ Reconcile branch trusted caller-supplied `_role` → an active kid could mint a parent `user_roles` row (escalation via `has_role`, which reads `user_roles` not `profiles.role`). Live + reachable: `EXECUTE` held by `anon, authenticated, service_role, sandbox_exec, postgres`.
- ✅ Fixed: reconcile now sources role from the trigger-protected `profiles.role`. Verified by re-reading the branch. See `decisions.md`.

**Admit/deny enum crash — found & fixed**
- ✅ `admit_pending_member` / `deny_pending_member` inserted `activity_log.significance = 'notable'`; the enum only permits `'routine' | 'milestone'`. Every admit/deny failed server-side. Found by Code during live testing, confirmed via network trace.
- ✅ Fixed to `'milestone'` in both functions. **This shipped inside the 07-14 data layer the security audit had already passed — the audit checked security, not enum validity. Verified-secure ≠ verified-functional.**

**`recurrence_day` prod crash — found (real user) & fixed (`172a07f`)**
- ✅ Quest creation failed in prod with "Could not find the 'recurrence_day' column" — **core loop down for real users** (May hit it; Scott reproduced). Cause: the 07-14 recurrence rework dropped the column in the DB but the matching frontend removal was **written, stashed, and never committed** — the log said "removed from both frontend forms"; the code disagreed.
- ✅ The fix was the parked stash. Applied onto a resynced tree (local was 3 commits behind), committed `172a07f`, pushed, verified from outside, deployed, **verified by Scott reproducing-then-not-reproducing on the live app.**

**Pending-member roster leak + switch-target GAP — found (Scott, using the app) & fixed (`b31c92c`)**
- ✅ Pending members rendered in the Briefing roster *and* the Profile switch picker (the roster is the walk-up picker), making a phantom pending card a tappable switch target with zero confirmation. Cause: `useFamilyMembers` filtered only on `family_id`, never on `status` — and RLS authorizes the *reader*, not the *target row's status*, so a pending row sailed through.
- ✅ Fixed: `useFamilyMembers` now selects `status` and filters `status='active'`. One hook, both surfaces. Pending members now appear only in the admissions queue. Verified from outside; `b31c92c`.

---

## 🟡 PENDING VERIFY

- 🟡 **`b31c92c` roster fix — deploy + eyeball.** Commit is on origin; confirm Lovable deployed and the phantom pending card is gone from the live roster. (Last-mile check, same discipline as the recurrence fix.)
- 🟡 **Recurrence chip legibility.** Anchors are invisible — a kid sees "MONTHLY" with no hint it means the 1st. `RECURRENCE_LABEL` enrichment ("Weekly · Mon" / "Monthly · 1st") scoped and skipped (exceeds one line). Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven on fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.

---

## ⬜ TWO BOARD BUGS STILL OPEN (surfaced 2026-07-14)

- ⬜ **The empty-board eulogy is a lie on a live board.** "All quiet at the hold" renders directly above "4 open" and a fresh completion. This is the doorway-not-eulogy fix, now with evidence.
- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 vocabulary bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit** — see critical path #1. Confirm harness reachability first (parking-lot).
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; display name **`Family-Quest-Board`** (scaffolding, exists nowhere in the product). Lands in spam. Templates in Lovable Cloud → Emails. Custom sender: DNS at Porkbun (free) + SMTP (Resend free tier). ⚠️ *Inspect any NS-record request before pasting — delegation ≠ a TXT record.*
- ⬜ **Service worker + app-shell cache** — no functional SW; makes "installable PWA" true instead of aspirational.
- ⬜ **Backup posture.** Backend is Lovable Cloud; backups/PITR/export are Lovable's to grant. Code has a Git backup; **the data has none.** Gate B blocker. (See parking-lot open decision.)
- ⬜ **Prod test-object cleanup.** `QA Parent` / `QA Joiner` / household `QA Verify Hold` — created **on the production backend** during 07-15 admit-flow testing (local dev points at the same DB as theemberhold.com — see parking-lot staging-DB decision). Plus older cruft: `Testi`, `testy`, `Daily test`, `iopuyiouh`, and orphaned auth users `scottydawg@gmail.com`, `scott.draper83+alpha@gmail.com`. Data, not code.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport.** **Tabled by Scott.** Full scope in parking-lot: hand-cut roster, file drop (Haiku), rewrite `HERO_AVATARS`, land `Feast→Hall`, retire `avatar-review.tsx`, invalidate stale `hero:` picks (the re-forge migration), re-lock the free/paid split (the 16/24 math predates the 48-roster). Do not push; surface when Scott calls it.
- ⬜ **Pip first-run onboarding screens** + the empty-board doorway fix. *Gated behind P4×L8.*
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Quick Add default EXPANDED on an empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **List suggestions in onboarding**.

---

## 🅿️ PARKED

See `parking-lot.md`. Display/wall mode (the defensible morale pull-forward) · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · Ranks as a household dial · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · recurrence miss-asymmetry · recurrence chip legibility.

---

## 🔴 OPEN RECON RESOLVED THIS SESSION — the walk-up trust boundary

The pending-roster recon (which produced the `b31c92c` fix) also examined the profile-switch / PIN flow. Findings: switching into a PIN-less adult profile **hard-gates** correctly (session never fires ahead of PIN-set success); Cancel returns to the picker (no bypass). **But** the "active member" switch is **cosmetic, not a security boundary** — on a shared device signed into a parent's account, physical possession = parent authority, so a kid holding the unlocked device can set/reset an adult PIN (the server correctly sees a parent session). This is the walk-up kiosk model working as designed, consistent with the 2026-07-10 kid-auth DECLINED decision. **Accepted for household use; logged as a live input to the P4×L8 audit for the distribution-grade version.** See `decisions.md` (NOTED, revisit trigger = P4×L8).

---

## 🔵 THE BUILD MODEL — holding, and stress-tested today

**Claude Code frontend build proven and repeating.** Today: resynced before touching a stale tree, stashed unrelated WIP instead of discarding it, caught its own codegen side-effect (`routeTree.gen.ts`) and restored it before staging, verified every push from outside, and stopped at scope edges for review. When auto-accept was accidentally left ON for the admit-button build, the result was still clean — the review simply moved from before-the-write to after — because the job was small and well-fenced. The lesson held: auto-accept OFF for security/`supabase/` work is about preserving the *review seam*, not distrust.

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's.
- **The live-DB audit method** (Lovable extracts read-only, an independent agent judges) is now the standard for data-layer security verification. See `decisions.md`.
- **Model routing:** Haiku (recon/greps/mechanical) · Sonnet (build jobs with latitude) · **Opus (the tenant-isolation audit only).**
- **One writer at a time.** Data-layer → Lovable; frontend → Code.
- **Still unproven:** whether Lovable *runs* an externally-pushed migration (the avatar `hero:` reset remains the cheap first test — tabled).

---

## ✅ SHIPPED — 2026-07-14

**Admit-on-approval data layer** (verified 07-15). **Hub open-bounty count** now matches the board via one shared predicate `isOpenBountyVisible()`. **Recurrence reworked to fixed calendar anchors** (weekly=Monday, monthly=the 1st, daily=tomorrow); `recurrence_day` dropped from DB/trigger — *frontend removal completed 07-15 (`172a07f`) after it turned out to have been stashed, not shipped.*

## ✅ SHIPPED — 2026-07-12 (day)

Claude Code proven as a build lane (feed vocabulary bundle `c9069a6`; `vault.tsx` dedup `7132605`; Lists collapsible `fb6aa99`). Auth: email confirmation required, holding-state signup, password reset verified, min length 6→8. Hold Alpha built (second isolated household — the P4×L8 instrument). 13 real accounts confirmed, not four.

## ✅ SHIPPED — 2026-07-11

Engine — daily respawn verified; lifecycle verified end-to-end. Vocabulary — `OPEN BOUNTY` chip killed; `XP` eliminated from install prompt. Render — safe-area inset fix. Avatars — roster regenerated (48 characters, 19 sheets, ~600px each). Claude Code stood up; **Lovable ↔ GitHub sync connected** — the codebase's first backup.

## ✅ SHIPPED — 2026-07-10

Engine — adult auto-approval · daily board fix · edit-form parity. Copy — XP killed. Vault kid view (dual-mode) + adult view (three zones) + couples rail (`reward.audience`). Quest audience filter + badges. Security — over-broad anon EXECUTE grant revoked. Docs migrated to this repo; `north-star.md` created.

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Rename never landed in code — still outstanding via avatar transport.*
- **2026-06-29 → 06-26** — Lists v1, invite/deep-link, notifications, PIN recovery, collapsible picker · activity feed spine · monthly recurrence + audience · Campaigns, Calendar, Briefing · the Vault, PIN, Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks, quest detail, `+` FAB, brand.

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-18 (evening)** — *avatar transport shipped, and a three-item QA burn-down.* The long-tabled Gate-A roster item came off the shelf and landed end-to-end: 48-avatar roster live, tier-tagged free/founder (16/32), founder gate built and dormant, re-forge run on the glass. Around it: three QA fixes shipped (Ranks per-member completed-quest expand, phone + wall; calendar month view now shows the whole visible month past + future, phone + wall; the "hero" vocab leak in the wall's claim picker killed). One QA item (#5 "hold admin role") reclassified as distribution-era support tooling, not a home-hold fix. One live defect found and fixed twice over: invalidated avatar picks were rendering their raw id as large text (the "ugly number") — a render-fallback ("the floor") is building at close to render a clean placeholder instead. Two real open findings surfaced (re-forge reach across the 13; render fallback pending glass-verify) plus four new P4×L8 audit inputs from the gate's new DB objects.

Last session (prior): **2026-07-18 (day)** — *doc-protocol repair, a ledger cleanup, and the wall's v1 commit capstone.* Opened by catching that 07-17's botched wrap had spawned an over-correction — a "Scott sets every status" rule + a compliance checkpoint — that mistook sloppiness for the cure and would have stripped jAIne's judgment from the model. Verified by live reconciliation that the poison never reached canon; restored the model and committed the catch-up (the six 07-17 decisions + the sync-to-origin rule folded into session-protocol). Then a live defect: SnowDad's Vault spendable read 0 despite 106 lifetime — root-caused to debit-side test cruft (three June self-redemptions of 150 > 106 earnings, clamped to 0), cleaned up. And the capstone: the wall's adult-verified turn-in commit, walked back from identity-bound to adult-verify once recon showed identity-bound attribution is a data-layer P4×L8 build.

Last session (prior): **2026-07-17** — *came in to "finish the wall display"; left with the wall feature-complete for Scott's own hold.* Full interaction layer built in one session (on-ramp, idle drift-home, responsive tiles, exit affordance, the complete propose tier, the Vault-in-popup, ~10s poll, seamless ticker, tappable calendar detail). The membrane held throughout — the wall proposes; it never mints, spends, approves, or edits.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, plus an ambient wall for the home hold, plus a live 48-avatar roster with a dormant founder gate.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), and now the tiered avatar system. A complete product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — one gate left before distribution.** #1 (join-code bypass, CRITICAL) fully CLOSED; #5, #6, #8 all closed. The remaining gate is the **P4×L8 tenant-isolation audit** (the mortal-peril item). *Caveats carried forward: verified-secure ≠ verified-functional; live-schema-drift means the audit reads the live grant surface, not the repo. New inputs from the wall (`wall_request_redemption`, commit-attribution class) and now from the avatar gate (see below).*

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. Gated behind distribution.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1).** Opus, auto-accept OFF. The mortal-peril item and the last security gate before distribution. Hold Alpha exists — run it as an attack. Banked inputs: 07-15 live-catalog dump · 25 linter warnings · 07-16 Data-API grant surface · `wall_request_redemption` household-scope · commit-attribution class (`approved_by` client-asserted, `decided_by` session-locked) · **(new 07-18 evening) the founder-gate DB objects — see the four inputs below.** First: confirm the adversarial harness can reach Lovable Cloud's DB from outside (parking-lot open decision) — if not, the audit degrades to policy-reading. | Distribution. Gate B. |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix | Gate D. Activation. *Gated behind #1.* |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen already received it. |
| **4** | **Avatar transport — ✅ SHIPPED 2026-07-18 (evening).** Roster live, tiered, gate dormant, re-forge run. Remaining avatar work is downstream: the paywall flip (two data changes, when Scott's ready) and Stripe checkout (= #5 below). | — |
| **5** | **Founding Guildhall build** (Stripe + webhook + entitlement write) | Money. The gate is the cosmetic half; this is the "a stranger can actually buy in" half. Decoupled from the gate on purpose. |

**Next-action fork (Scott's call):** honest sequence remains **P4×L8 first** — it's the gate, everything downstream sits behind the distribution it unblocks. The contained non-Opus win is still **the auth email**.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-18 (evening)

**Avatar transport — shipped end-to-end.**
- ✅ 48-roster (`forge/garden/keep/hall` 01–12) copied into `src/assets/avatars/`, committed (`Add 48-avatar roster…`), pushed, GitHub→Lovable sync confirmed. Old `avatar-01…21` left in place (untouched, not deleted — still referenced by `avatar-review.tsx`).
- ✅ Catalog rewritten to the 48; every avatar tier-tagged **free/founder** per the 16/32 split (4 free + 8 founder per class).
- ✅ `families.is_founder` boolean added (default false, none backfilled). Household-level entitlement; never per-member (honors the seam rule).
- ✅ Global gate stored as a **DB value** — `public.system_flags.founder_gate_enabled` (seeded false), read via `founder_gate_enabled()`. **The flip is a one-line UPDATE, not a build.** Household read via `my_household_is_founder()`.
- ✅ Picker shows all 48; founder tiles render locked (dim + ember-lit lock badge, disabled) ONLY when gate ON and household not founder. Gate OFF today → nothing locked, all selectable. Locked-state UI built dormant so the future flip Just Works.
- ✅ **Re-forge:** 28/28 existing profiles invalidated (26 old `hero:NN`, 2 non-hero) → routed to `/reforge-avatar` on next authenticated nav, single-prompt pick with an on-theme "re-forged" line. **Scott ran it live** — forced to re-pick on refresh, then set the rest of his hold manually.
- ✅ Roster art verified on the glass (Scott's eyes): all four class columns read correctly, the wide luminosity band cohered (chibi → painterly on one floor). `keep-10` (free) is the lowest-quality export (near-black) — Scott running with it; rework only if it fails on real glass.

**QA burn-down — three shipped, verified on the glass:**
- ✅ **Ranks: expand a member → their completed quests.** Phone + wall; shared component carried both in one cycle. Read-only. *(QA #1)*
- ✅ **Calendar month view shows the whole visible month, past + future.** Phone + wall; shared query, one fix covered both. Was clamping today-forward. *(QA #3)*
- ✅ **Wall "hero" vocab leak killed.** Off-vocabulary word removed from the wall's open-bounty claim picker; copy-only, identifiers untouched. *(QA #2)*

**QA #5 reclassified** — "hold admin role" clarified as a cross-hold super-admin / tier-2 support role (reach into a family's hold to fix members/events/feed). Cross-tenant → distribution-era (Gate C–E), not a home-hold QA item. Residual ambiguity flagged (in-hold admin tier vs cross-hold super-admin are different builds); Scott to bring concrete examples. Parked. See `decisions.md`.

---

## 🟡 PENDING VERIFY

- 🟡 **Avatar render fallback ("the floor") — building at close.** Frontend-only: any unresolved/invalid avatar pick renders a clean ember-lit placeholder, NEVER the raw key. Fixes the confirmed ugly-number render. **Glass test:** pull up a not-yet-re-forged member, confirm a clean placeholder not a number, and check the **wall** specifically (always-on surface where it was worst). Report was to list every render site touched + whether they share one fallback path.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** With the gate OFF you cannot see free vs founder — all look selectable. The ONLY way to confirm the 16/32 tagging landed is to briefly flip the gate ON, confirm exactly **32 lock / 16 open**, flip back OFF. A mis-tag is a silent revenue leak or a locked-out freebie. Not urgent; can't be done gate-off.
- 🟡 **Routing fix (`28ab40d`) — live repro.** Sign out from a pending account's waiting screen, sign back in, confirm you land on the waiting screen (not setup) and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** `RECURRENCE_LABEL` enrichment ("Weekly · Mon" / "Monthly · 1st") scoped, skipped (exceeds one line). Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit** (built 07-18 day). Glass test: awaiting-approval quest → Approve → PIN → wrong/kid attempt mints nothing → correct session-owner PIN commits and balance moves → `approved_by` lands as session-owner, not the tapped tile.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device; confirm the wall never exposed a commit control it shouldn't.

---

## ⬜ OPEN FINDINGS — from the avatar session

- ⬜ **Re-forge reach across the 13 households (real defect, later fix).** Re-forge only prompts the member who logs in; other members' picks are invalidated but nobody re-prompts them — they render the fallback placeholder (once the floor ships) until an adult manually fixes each. Invisible from the owner seat (Scott speed-fixed all four). A rough first impression when this rolls to testers. Options: prompt each member on next active session, OR a hold-owner "N hearthmates need re-forging" nudge. **A calm, later decision — matters when re-forge reaches the 13, not before.** The floor (above) defuses the ugliness in the meantime.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align.

---

## 🟢 SHIPPED — 2026-07-18 (day)

**Doc-protocol repair + catch-up committed.** 07-17's over-correction ("Scott sets every status" + a compliance checkpoint) reconciled as never-canon; model restored (jAIne sets statuses as judgment, Scott overrules); six 07-17 decisions committed; sync-to-origin rule folded into `session-protocol`.

**SnowDad Vault spendable = 0 — diagnosed & cleaned (data, not code).** Debit-side test cruft: three June self-redemptions (150) > 106 earnings → `member_spendable` clamped to 0. 3 redemption rows + 1 orphaned `activity_log` row deleted; reconciled to 106. Two real findings logged (below).

**Two findings from the cleanup (logged, not yet fixed):**
- ⬜ **`parent_self_redeem` doesn't write `activity_log` — feed blind spot.** On the wall the ticker is load-bearing, so this is real. NOTED.
- ⬜ **`member_spendable` silently clamps overdraw to 0** (P4×L8 input). 150 spent against 106 was allowed and hidden. NOTED.

**Wall adult-verified turn-in commit** — built (🟡 glass-verify pending, above).

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit** — critical path #1. Confirm harness reachability first. Banked inputs list above.
- ⬜ **NEW P4×L8 inputs — the founder-gate DB objects (gate is OFF, nothing on fire; log for the audit):**
  - **`system_flags` WRITE access — confirm before the gate ever matters.** The build report covered read, not write. If an authenticated household user can UPDATE that table, a random user could flip the global paywall. Only `service_role` should write it.
  - **`founder_gate_enabled()` grants EXECUTE to anon — likely over-broad.** Same pattern Scott revoked three weeks ago (over-broad anon EXECUTE). A logged-out user has no household and can't pick an avatar; probably tighten to authenticated.
  - **Two new SECURITY DEFINER reads** — `founder_gate_enabled()`, `my_household_is_founder()` — confirm household scope where relevant.
  - **`families.is_founder`** — confirm read/write scope; it's the entitlement Stripe will eventually write.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; display name `Family-Quest-Board`. Lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. Gate B blocker.
- ⬜ **Prod test-object cleanup.** `QA Parent` / `QA Joiner` / `QA Verify Hold`, older cruft, orphaned auth users, the `+ca@gmail.com` / "Testies" test hold. Data-hygiene pattern noted: **deleting a quest does not clean up redemptions it funded** — reconcile, don't just delete.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. *Gated behind P4×L8.*
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first; the wall inherits it. On the wall it's an on-behalf write (`wall_toggle_favorite`, another SECURITY DEFINER / P4×L8 line).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.

*(Avatar transport removed from this list — SHIPPED 2026-07-18 evening.)*

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** (board bug above) · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** (shrinks the wall's empty-Vault case) · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7 — parked, data-layer build) · **Calendar alerts** (QA #6 — needs push-vs-in-app decision; push is fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint**.

---

## 🅿️ PARKED

See `parking-lot.md`. **QA #5 super-admin / tier-2 support role** (new 07-18) · **Re-forge reach across the 13** (new 07-18) · Favorites on the wall · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 2026-07-17)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. The wall applies this to a display: cosmetic tiles on one shared parent session, propose actions unguarded, commit actions behind the real adult PIN. Accepted for household use. See `decisions.md` (NOTED, revisit trigger = P4×L8).

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first (LOCKED 07-17, folded into session-protocol 07-18).
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit only).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed: data layer, frontend, live-DB verification, one adjacent escalation. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member by construction. Full RPC contract + verification on record.

---

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster/switch-picker "no members" root-caused to zero Data-API grants for `authenticated` (fourth live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). "All quiet at the hold" resolved working-as-intended.

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

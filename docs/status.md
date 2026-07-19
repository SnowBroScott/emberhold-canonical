# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-19 (day)** — *Lovable's 8 scanner findings triaged end-to-end; the P4×L8 reachability question answered and the authenticated attack fully briefed & queued.* Ran Code (recon seat) over all 8 findings. Two of the three "Criticals" were our own safeguards holding — false positives. Four are real (one is the already-accepted walk-up boundary; three are genuine hygiene fixes). One is real RLS looseness mitigated by a trigger whose live attachment still needs confirming. Verdicts below; **no exploit specifics in this doc — the repo is public.** The larger correction: the standing "can the harness even reach the live DB?" caveat is **resolved** — Code self-provisions two confirmed households via the browser tool (proven 07-16: real signup → email-confirm in-tab → captured JWT → admit/deny across two accounts). Today's instance failed only because it worked from a bare curl/anon seat and self-declared "degraded to policy-reading" — a false limit, not a stack limit. The P4×L8 brief now **mandates the browser tool and removes the fork**; it's Opus, auto-accept OFF, its own window, blocked only on the 5-hour tank refilling. Two build-model decisions came ready (decisions.md): the trust-exception for high-stakes attacks, and the browser-provisioning rule.

Last session (prior): **2026-07-19 (morning)** — *wall display-mode fine-tune, shipped and verified on the glass; a monetization riff hardened into a real SKU shape.* Two wall tweaks landed end-to-end on the physical iPad. "Who's on the Hearth" avatars grew ~18% of tile width; the quest-count two-line wrap was killed for good. Two durable wall-rendering rules logged (NBSP-not-nowrap across a flex boundary; the halo/padding tax on any wall avatar resize). The theme-pack monetization idea hardened from "palette packs" into **living-hold ambient themes** — parked with receipts.

Last session (prior): **2026-07-18 (evening)** — *avatar transport shipped, and a three-item QA burn-down.* 48-avatar roster live, tier-tagged free/founder (16/32), founder gate built and dormant, re-forge run on the glass. Three QA fixes shipped. One live defect (the "ugly number" render) surfaced → render-fallback ("the floor") built at close. Two real open findings + four new P4×L8 audit inputs from the gate's new DB objects.

Last session (prior): **2026-07-18 (day)** — *doc-protocol repair, a ledger cleanup, and the wall's v1 commit capstone.* Caught 07-17's over-correction before it reached canon; model restored; six 07-17 decisions + the sync-to-origin rule committed. SnowDad Vault spendable = 0 root-caused to debit-side test cruft and cleaned. Wall adult-verified turn-in commit built (glass-verify pending).

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, plus an ambient wall for the home hold, plus a live 48-avatar roster with a dormant founder gate.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), and now the tiered avatar system. A complete product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — one gate left before distribution.** Lovable's 8 scanner findings are now triaged (07-19, verdicts below): two false positives, four real (one = the accepted walk-up boundary, three = queued hygiene fixes), one RLS/trigger item to confirm live. **None of them breached — and none of them *tested* — the cross-tenant boundary.** The remaining gate is still the **P4×L8 tenant-isolation audit** (the mortal-peril item). It is now **fully briefed and reachability-solved** (authenticated two-household browser-provisioned attack); it awaits only an Opus window with a full tank. *Caveats carried forward: verified-secure ≠ verified-functional; live-schema-drift means the audit reads the live grant surface, not the repo.*

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. Gated behind distribution.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1).** Opus, auto-accept OFF, own window. The mortal-peril item and the last security gate. **Now fully briefed as an authenticated two-household cross-tenant attack; reachability is solved (browser tool, self-provisioned confirmed accounts — proven 07-16).** The brief mandates the tool and removes the path-choice fork. Banked inputs: 07-15 live-catalog dump · 25 linter warnings · 07-16 Data-API grant surface · `wall_request_redemption` household-scope · commit-attribution class (`approved_by` client-asserted, `decided_by` session-locked) · the founder-gate DB objects (below; two now confirmed-live) · the 8 scanner findings (triaged 07-19, below) · **the #3 quest-trigger live-attachment check (folded into Step 2h of the attack).** Blocked only on the 5-hour window refill. | Distribution. Gate B. |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix | Gate D. Activation. *Gated behind #1.* |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen already received it. |
| **4** | **Avatar transport — ✅ SHIPPED 2026-07-18 (evening).** Roster live, tiered, gate dormant, re-forge run. Downstream: the paywall flip (two data changes) and Stripe checkout (= #5). | — |
| **5** | **Founding Guildhall build** (Stripe + webhook + entitlement write) | Money. Decoupled from the gate on purpose. |

**Next-action:** the interim scanner triage is **done** — the honest sequence is now **P4×L8 itself**, teed up and waiting on the tank. The contained non-Opus win remains **the auth email**. The three real hygiene fixes (#5/#6/#7 below) are queued but do **not** move the gate; sequence them when convenient, not ahead of the attack. **Credits ~1.8** — the fixes are almost all DB-lane (route through Lovable), except #5 which is a credit-free Code frontend commit.

---

## 🟢 SECURITY TRIAGE — Lovable's 8 scanner findings — DONE 2026-07-19 (day)

*Verdict-level only. Exploit mechanism and attack detail live in the Workstream 1 / Code session, never here (public repo). Recon-seat pass — the confirmed-real items still want the authenticated attack to close them fully.*

**False positives — our safeguards held (vindicated):**
- ✅ **"Forgot PIN" takeover (was CRITICAL) — FALSE POSITIVE.** The reset RPC is parent-role + same-household gated server-side; anon is denied. *(Minor real note: the confirm() copy claims "only the account owner" — inaccurate; any parent-role session in the household can reset. UX-copy fix, not the claimed vuln.)*
- ✅ **Join-code → full Parent admin (was CRITICAL) — FALSE POSITIVE.** The join path hardcodes `role='kid' / status='pending'`; the caller's requested role is stored only as a hint; real tier is set by the parent-gated `admit_pending_member`. This is the 07-15 anti-escalation wiring, confirmed.
- ✅ **Broad SECURITY DEFINER execute surface (#8) — mostly CLOSED.** Sensitive definer RPCs are correctly anon-denied or internally guarded, except the founder-gate exception (#7 below).

**Real — queued fixes (none move the P4×L8 gate; all are edge hygiene):**
- ⬜ **Adult PIN plaintext in `localStorage` during signup — CONFIRMED, contained. CREDIT-FREE.** The PIN persists in plaintext for the whole email-confirmation window. Fix: don't persist it client-side; collect it in the post-confirmation finish-setup step. **Frontend-only → Code commit, no Lovable credit.** Lowest severity of the batch but the only free one.
- ⬜ **Kids can read `adults_only` reward names/costs — CONFIRMED.** The rewards SELECT policy filters by family only; the adults-only hiding is client-side React filtering. Fix: add an audience predicate to the SELECT policy (DB-lane / Lovable). The one with a real "a person would notice this" quality.
- ⬜ **`founder_gate_enabled()` + `my_household_is_founder()` executable by anon — CONFIRMED live drift.** The over-broad-anon EXECUTE pattern revoked ~3 weeks ago, re-introduced (and `my_household_is_founder` leaks via PUBLIC's default execute never being revoked). Low blast radius (a global boolean; the household read returns false to anon) but it's exactly the pattern we already killed once. Fix: revoke anon/PUBLIC execute, keep `authenticated` (DB-lane / Lovable).

**Real but = the already-accepted boundary:**
- 🔵 **Tier boundary is frontend-only in the shared-session model — CONFIRMED, KNOWN-ACCEPTED.** A device-kid rides the owner's ambient parent JWT; economic RPCs gate on `has_role(parent)`, which that JWT satisfies. This is the **walk-up trust boundary** (recon 07-15, NOTED, revisit-trigger = P4×L8) — **intra-household, not cross-tenant.** No cross-tenant path (`current_family_id()` is derived server-side from `auth.uid()`). The revisit trigger has effectively fired; the "own-session vs per-member-auth" decision is the real fork it raises (still Scott's, still parked).

**Real RLS looseness, mitigated in code — confirm the mitigation is live:**
- 🟡 **Quests UPDATE policy is column-unrestricted (#3).** The row-writability claim is true; the mint block rests entirely on the `a_enforce_quest_update_authority` BEFORE-UPDATE trigger (blocks non-parents flipping status→approved / changing points/approved_by). Embers are computed (a SUM), not stored — so "minting" == getting a quest to approved, which the trigger refuses for a genuine kid role. **Unconfirmed: is the trigger actually attached in the deployed DB?** (The drift question the recon seat couldn't close.) → folded into the P4×L8 attack (Step 2h); defense-in-depth, also tighten the RLS WITH CHECK so it doesn't rest on the trigger alone.

**Dependency scan:**
- 🟡 **npm audit = 0 against `package-lock.json`; inconclusive for the real dep set.** The project's actual lockfile is `bun.lock` (bun not installed in the recon env). Run `bun audit` against `bun.lock` to reproduce the scanner's "1 vuln / 55 packages." Low priority.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning)

**Wall display-mode fine-tune — shipped, verified on the physical glass (Scott's eyes, real iPad).**
- ✅ **"Who's on the Hearth" avatars enlarged.** Wall avatar wrapper `min(66%, 11.5rem)` → `min(78%, 13rem)` (~18% of tile width), ring offset `inset-[-4px]` → `inset-[-2px]`, tile padding tightened (`sm:p-3`→`sm:p-2`, gap `gap-1.5`→`gap-1`) so the size isn't re-consumed by halo/padding. Avatar now reads as the dominant element.
- ✅ **Quest-count line wrap killed.** Root cause: `whitespace-nowrap` on the parent `<p>` was lost across the flex-item boundary and iPad Safari broke the internal space. Fix: NBSP between the label words + `whitespace-nowrap` on both inner spans + `flex w-full min-w-0` / `items-baseline`. One centered line now.
- Scope held: `WallMemberTile` only; the `Avatar` component untouched.
- **Two durable wall-rendering rules captured** (decisions.md): NBSP-not-nowrap for wall text that must never break; the halo/padding tax on any wall avatar resize.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-18 (evening)

**Avatar transport — shipped end-to-end.**
- ✅ 48-roster (`forge/garden/keep/hall` 01–12) into `src/assets/avatars/`, committed, pushed, sync confirmed. Old `avatar-01…21` left in place (still referenced by `avatar-review.tsx`).
- ✅ Catalog rewritten to the 48; tier-tagged free/founder per the 16/32 split (4 free + 8 founder per class).
- ✅ `families.is_founder` boolean added (default false). Household-level; never per-member (honors the seam rule).
- ✅ Global gate stored as a **DB value** — `public.system_flags.founder_gate_enabled` (seeded false), read via `founder_gate_enabled()`. **The flip is a one-line UPDATE, not a build.** Household read via `my_household_is_founder()`.
- ✅ Picker shows all 48; founder tiles render locked ONLY when gate ON and household not founder. Gate OFF today → all selectable. Locked-state UI built dormant.
- ✅ **Re-forge:** 28/28 existing profiles invalidated → routed to `/reforge-avatar` on next authenticated nav. **Scott ran it live.**
- ✅ Roster art verified on the glass. `keep-10` (free) is the lowest-quality export (near-black) — running with it; rework only if it fails on real glass.

**QA burn-down — three shipped, verified on the glass:**
- ✅ **Ranks: expand a member → their completed quests.** Phone + wall. *(QA #1)*
- ✅ **Calendar month view shows the whole visible month, past + future.** Phone + wall. *(QA #3)*
- ✅ **Wall "hero" vocab leak killed.** Copy-only. *(QA #2)*

**QA #5 reclassified** — "hold admin role" = a cross-hold super-admin / tier-2 support role. Cross-tenant → distribution-era (Gate C–E), not a home-hold QA item. Parked. See `decisions.md`.

---

## 🟡 PENDING VERIFY

- 🟡 **Avatar render fallback ("the floor") — building at close.** Frontend-only: any unresolved/invalid avatar pick renders a clean ember-lit placeholder, NEVER the raw key. **Glass test:** pull up a not-yet-re-forged member, confirm a clean placeholder not a number, check the **wall** specifically. Report should list every render site touched + whether they share one fallback path.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Gate OFF → all look selectable. Briefly flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF. A mis-tag is a silent revenue leak or a locked-out freebie.
- 🟡 **Routing fix (`28ab40d`) — live repro.** Sign out from a pending account's waiting screen, sign back in, confirm you land on the waiting screen (not setup) and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** `RECURRENCE_LABEL` enrichment scoped, skipped (exceeds one line). Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit** (built 07-18 day). Glass test: awaiting-approval quest → Approve → PIN → wrong/kid attempt mints nothing → correct session-owner PIN commits and balance moves → `approved_by` lands as session-owner, not the tapped tile.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device; confirm the wall never exposed a commit control it shouldn't.

---

## ⬜ OPEN FINDINGS — from the avatar session

- ⬜ **Re-forge reach across the 13 households (real defect, later fix).** Re-forge only prompts the member who logs in; others' picks are invalidated but nobody re-prompts them — they render the fallback placeholder until an adult manually fixes each. Invisible from the owner seat. Options: prompt each member on next active session, OR a hold-owner "N hearthmates need re-forging" nudge. **A calm, later decision — matters when re-forge reaches the 13.** The floor defuses the ugliness meanwhile.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align.

---

## 🟢 SHIPPED — 2026-07-18 (day)

**Doc-protocol repair + catch-up committed.** 07-17's over-correction reconciled as never-canon; model restored; six 07-17 decisions committed; sync-to-origin rule folded into `session-protocol`.

**SnowDad Vault spendable = 0 — diagnosed & cleaned (data, not code).** Debit-side test cruft: three June self-redemptions (150) > 106 earnings → clamped to 0. Reconciled to 106. Two findings logged (below).

**Two findings from the cleanup (logged, not yet fixed):**
- ⬜ **`parent_self_redeem` doesn't write `activity_log` — feed blind spot.** On the wall the ticker is load-bearing. NOTED.
- ⬜ **`member_spendable` silently clamps overdraw to 0** (P4×L8 input). 150 spent against 106 was allowed and hidden. NOTED.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit** — critical path #1. **Fully briefed; reachability solved (browser tool).** Awaiting an Opus window with a full tank. Banked inputs list above.
- ⬜ **Three real scanner-fix hygiene items (queued, do NOT precede the attack):** PIN-plaintext-in-localStorage (credit-free Code frontend) · adults-only rewards SELECT-policy audience predicate (Lovable/DB) · revoke anon/PUBLIC execute on `founder_gate_enabled` + `my_household_is_founder` (Lovable/DB). The two DB-lane fixes should land as **one bundled migration**, auto-accept OFF, as repo migration files (not hand-applied drift). ⚠️ *Exploit specifics stay in the Code session — never this repo.*
- ⬜ **Founder-gate DB objects — P4×L8 inputs (status updated 07-19):**
  - **`founder_gate_enabled()` anon EXECUTE — CONFIRMED live over-broad** (see triage #7). Fix queued.
  - **`my_household_is_founder()` anon-executable via PUBLIC default — CONFIRMED live drift** (see triage #7). Fix queued.
  - **`system_flags` WRITE access — STILL TO TEST.** Can an authenticated household user UPDATE it and flip the global paywall? Only `service_role` should write. → P4×L8 attack Step 2g.
  - **`families.is_founder` read/write scope — STILL TO TEST.** The entitlement Stripe will eventually write. → P4×L8 attack Step 2g.
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; display name `Family-Quest-Board`. Lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. Gate B blocker.
- ⬜ **Prod test-object cleanup.** `QA Parent` / `QA Joiner` / `QA Verify Hold`, older cruft, orphaned auth users, the `+ca@gmail.com` / "Testies" test hold. Data-hygiene pattern noted: **deleting a quest does not clean up redemptions it funded** — reconcile, don't just delete. *(The P4×L8 attack will add Alpha/Bravo throwaway holds; its brief ends with a teardown manifest — Scott's cleanup is cheap and accepted.)*

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. *Gated behind P4×L8.*
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first; the wall inherits it. On the wall it's an on-behalf write (`wall_toggle_favorite`, another SECURITY DEFINER / P4×L8 line).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** (board bug above) · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7 — parked, data-layer build) · **Calendar alerts** (QA #6 — push-vs-in-app decision; push is fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Living-hold theme packs — monetization SKU #2** (07-19) · **QA #5 super-admin / tier-2 support role** (07-18) · **Re-forge reach across the 13** (07-18) · **Own-session vs per-member-auth** (the fork the walk-up boundary raises) · Favorites on the wall · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 2026-07-17, confirmed 2026-07-19)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs (which gate on `has_role(parent)`) are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`, so it does not cross the tenant boundary. Accepted for household use. The scanner independently re-found this (07-19 triage, finding #4); the revisit-trigger (P4×L8) has effectively fired, raising the own-session-vs-per-member-auth fork (parked). See `decisions.md` (NOTED, revisit = P4×L8).

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first (LOCKED 07-17, folded into session-protocol 07-18).
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."** (LOCKED-proposed 07-19.) Code has the skills; left to choose a path it can pick an unsanctioned one and self-declare a false limit. Mandate the tool, sequence, surface, and report; leave latitude only where jAIne is blind (the live-schema exploit execution).
- **Authenticated live-DB audit briefs MUST mandate the browser tool for account provisioning.** (NOTED-proposed 07-19.) Reachability was never a stack limit — the browser signup→confirm→JWT path has existed since 07-16. Instruct the path; don't assume it.
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit only).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed: data layer, frontend, live-DB verification, one adjacent escalation. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member by construction. *(Re-confirmed 07-19: the scanner's "join-code → parent admin" claim is a false positive against this wiring.)*

---

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster/switch-picker "no members" root-caused to zero Data-API grants for `authenticated` (fourth live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). "All quiet at the hold" resolved working-as-intended. *(Also the session that proved Code can browser-provision confirmed test households end-to-end — the reachability precedent cited 07-19.)*

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

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-17** — *came in to "finish the wall display"; left with the wall feature-complete for Scott's own hold.* The whole interaction layer got built in one long session: the display-mode on-ramp (profile entry → adult PIN → agenda/calendar launch selector), idle drift-home to a named resting state, responsive avatar tiles, an exit affordance (hold lockup → PIN → out), the **complete propose tier** (claim a bounty · turn in a quest · request a reward), the Vault relocated into the member popup (affordable-only, audience-filtered, motivating empty state), a decoupled ~10s data poll, a seamless feed ticker, and tappable view-only calendar event detail with member-color pills. **The membrane held through all of it** — the wall proposes; it never mints, spends, approves, or edits. Two recons this session paid rent: one confirmed on-behalf claim/turn-in needs no new data path (the parent-role RLS exemption already covers it), one caught that the canonical docs still described the wall as *fenced / never built* — **the docs had diverged from the live codebase on a load-bearing surface** (a fresh Code clone was 78 commits behind origin; see the LOCKED local-clone rule in decisions). This status is the reconciliation.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, plus an ambient wall for the home hold.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the hardened pending→admission routing path — **and now the wall/display mode** (feature-complete for own-hold, see below). A complete product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps:

> **Security — one gate left before distribution.** #5 and #8 patched by a June migration; #6 (kid self-approval) CLOSED; **#1 (join-code bypass, CRITICAL) fully CLOSED**. The remaining gate is the **P4×L8 tenant-isolation audit** (the mortal-peril item). *Caveats carried forward: verified-secure ≠ verified-functional; the migration files do not fully describe the live DB (four live-schema-drift instances on record), so the audit reads the live grant surface, not the repo.* **New 2026-07-17 input:** the wall added a second bespoke SECURITY DEFINER write (`wall_request_redemption`) — a named audit line item (see below).

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. Gated behind distribution.

See `north-star.md` for the gate ladder. **Note the ladder shift (2026-07-17):** display/wall mode was fenced as a post-strangers retention feature. It has now been **built for Scott's own hold** as the sanctioned fenced-delight morale pull-forward (it needs no security gate — it's for the home hold, not strangers). It remains fenced *for distribution* — the strangers-grade version still wants deployment hardening and the P4×L8 pass on its new write surface.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1).** Opus, auto-accept OFF. The mortal-peril item and the last security gate before distribution. Hold Alpha exists — run it as an attack. Banked inputs: 07-15 live-catalog dump · 25 linter warnings · the 07-16 Data-API grant surface (a hole that silently DENIED) · **(new 07-17) `wall_request_redemption`, a SECURITY DEFINER RPC — confirm it enforces household scope on the picked member_id.** **First: confirm the adversarial harness can reach Lovable Cloud's DB from outside** (parking-lot open decision) — if not, the audit degrades to policy-reading. | Distribution. Gate B. |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix | Gate D. Activation. *Gated behind #1.* |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen people already received it. |
| **4** | **Avatar transport** — *generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain.* **Tabled by Scott.** | The Guildhall, the delight layer |
| **5** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

**Next-action fork (Scott's call):** the honest sequence is **P4×L8 first** — it's the gate, and everything downstream sits behind the distribution it unblocks. The wall is now *built* for the home hold, so the "fun morale pull-forward" slot it used to occupy is spent — the remaining low-stakes contained win is **the auth email** (real users on the wrong end of it right now, no Opus window).

---

## 🟢 THE WALL / DISPLAY MODE — FEATURE-COMPLETE FOR OWN-HOLD (2026-07-17)

A single new file: `src/routes/_authenticated/wall.tsx`. Mountable ambient display; ~90% reuse of existing surfaces. **Propose-only trust model** — the wall shows and proposes; the adult commit gate (mint/spend/approve, real PIN) is untouched and unreachable from the wall. See the LOCKED decisions (2026-07-17) for the full trust model.

**✅ Built + human-verified this session:**
- ✅ **On-ramp** — profile-screen Display Mode entry (adult-gated) → adult PIN → launch selector (Agenda / Calendar, ephemeral per-launch choice, persisted nowhere) → wall mounts in the chosen resting state.
- ✅ **Idle drift-home** — any deviation (zone expanded, view toggled) decays back to the named resting config after a quiet idle (~60s, tunable). This is what makes it a *display*, not a left-on tablet.
- ✅ **Responsive tiles** — "Who's on the Hearth" tiles scale to container; hold together down to 4:3 ~1024×768; scaling freed room for member NAMES to show, not just faces.
- ✅ **Exit affordance** — hold lockup (top-left) → "Leave display mode?" → adult PIN → out. Mirror of entry.
- ✅ **Propose tier — claim + turn-in.** From Open Bounties: claim routes through a "Who's claiming this?" member picker (the banner has no tile-tap to source identity, so the pick IS the identity step — Law #1). Turn-in flows through the tapped tile (identity already established) and submits to the existing adult queue. No new data path — the parent-role RLS exemption already permits on-behalf claim/submit (07-17 recon).
- ✅ **Propose tier — redemption.** Vault lives inside the member popup (behind the tile tap): shows member's spendable balance + a Vault button that swaps the popup body to that member's rewards. **Affordable-only** (member_spendable ≥ cost), **audience-filtered** (adult/kid via reward.audience), **motivating empty state** ("No rewards in reach yet — complete a quest…"). Redeeming calls `wall_request_redemption` → PENDING request in the adult queue → **embers only leave on adult approval** (no wall debit).
- ✅ **~10s interval poll**, decoupled from the ticker. Silent in-place refresh; never remounts the ticker; open popups survive a refresh. NOT realtime/websocket.
- ✅ **Seamless feed ticker** — doubled strip, animates to one copy's width (geometry fix: the earlier `min-w-full` broke the −50% relationship). No flicker, no clipped first/last item.
- ✅ **Calendar event detail + color pills** — wall event pills are now tappable → open the existing `EventDetail` sheet in place; pills carry `memberColor()` (identity color, NOT a "class" system — that was a myth, corrected in decisions). **View-only on the wall:** Edit/Delete suppressed (canEdit() is always true under the shared parent session; destructive edits must not sit on an open kiosk).

**🟡 The one loop still worth walking on real glass:** the **full end-to-end membrane loop** — claim → turn-in → **approve on a SEPARATE device** → confirm the wall reflects it AND that the wall never offered an approve/mint/spend/edit control at any point. Individual pieces are verified; the single uninterrupted round-trip on the mounted iPad is the honest last check. Not blocking.

**🅿️ Still fenced for the DISTRIBUTION-grade wall:**
- Deployment on the real iPad mini — Safari version, PWA display-mode behavior, screen-sleep (Guided Access / never-sleep + dock), 4:3 ~1024×768. Real, deferred.
- P4×L8 pass on `wall_request_redemption` (household-scope check) — see critical path #1.
- Calendar event detail is BUILT; the member-color **event-pill work is done too**. (The old parking-lot "event detail + color pills, fenced together" line is now SHIPPED — removed from the fence.)

---

## 🟡 PENDING VERIFY (carried, non-wall)

- 🟡 **Routing fix (`28ab40d`) — live repro.** Sign out from a pending account's waiting screen, sign back in, confirm you land on the waiting screen (NOT setup) with a clean single-entry history; confirm auto-advance into the app the moment a parent admits. Code traced and closed it mechanically; needs the human repro. **(Open since 2026-07-16 — cheap, still uncaptured as done.)**
- 🟡 **Recurrence chip legibility.** Anchors invisible — "MONTHLY" with no hint it means the 1st. `RECURRENCE_LABEL` enrichment scoped and skipped. Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven on fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.

---

## ⬜ ONE BOARD BUG STILL OPEN

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 vocabulary bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align. *(Also in parking-lot polish burn-down.)*

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **P4×L8 tenant-isolation audit** — critical path #1. Confirm harness reachability first. Banked inputs: 07-15 live-catalog dump · 25 linter warnings · 07-16 Data-API grant surface · **07-17 `wall_request_redemption` household-scope check.**
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; display name `Family-Quest-Board` (scaffolding, exists nowhere in the product). Lands in spam. Custom sender: DNS at Porkbun (free) + SMTP (Resend free tier). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — no functional SW; makes "installable PWA" true instead of aspirational.
- ⬜ **Backup posture.** Backend is Lovable Cloud; backups/PITR/export are Lovable's to grant. **No direct Supabase access.** Code has a Git backup; **the data has none.** Gate B blocker. (Parking-lot open decision.)
- ⬜ **Prod test-object cleanup.** `QA Parent` / `QA Joiner` / household `QA Verify Hold`, plus older cruft (`Testi`, `testy`, `Daily test`, `iopuyiouh`), orphaned auth users, and any `+ca@gmail.com` / "Testies" / "Testicular" holds from routing tests. Data, not code.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport.** **Tabled by Scott.** Full scope in parking-lot. Do not push; surface when Scott calls it.
- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. *Gated behind P4×L8.*
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. **Now doubly load-bearing:** the wall's affordable-only Vault deliberately defers favorites *because* localStorage can't serve per-member on a shared wall. Build this as the phone-Vault's per-profile persistence job; the wall inherits it free. (See parking-lot: the empty-state message becomes a two-case branch once favorites exist.)
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** (see above) · **Quick Add default EXPANDED on an empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** (*now also relevant to the wall's empty-Vault state — a reachable floor reward shrinks the empty case*) · **Yearly/monthly event recurrence** · **List suggestions in onboarding** · **Wall ticker speed** (tune-by-eye legibility knob, not a rebuild) · **Wall calendar event-pill member color: dot-vs-full-tint** (both-ways look when revisited).

---

## 🅿️ PARKED

See `parking-lot.md`. **Favorites on the wall** (two-case empty-state branch, awaits per-profile persistence) · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · flat/peer holds · recurrence chip legibility · **the "how Scott & jAIne work" collaboration profile** (Scott's own Claude instructions/profile — a sober-daylight artifact, deliberately its own session, NOT a project doc).

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 2026-07-17)

Switching into a PIN-less adult profile hard-gates correctly. **But** the "active member" switch is **cosmetic, not a security boundary** — on a shared device signed into a parent's account, physical possession = parent authority. **The wall is this boundary applied to a display:** cosmetic member tiles on one shared parent session, propose actions unguarded, commit actions behind the real adult PIN. Accepted for household use; the wall's `wall_request_redemption` is logged as a live P4×L8 input for the distribution-grade version. See `decisions.md` (2026-07-17 wall trust-model + RPC entries).

---

## 🔵 THE BUILD MODEL — holding, and it caught two things today

- **Verify, don't guess — twice this session.** Recon before the claim/turn-in build confirmed no new data path was needed (saved a wrong build); recon before the calendar build found event-detail + color already existed (turned a "build" into a small wire-up). **Recon-first is the reason this session's builds mostly one-shot.**
- **The docs are not the live codebase — and can diverge silently.** The canonical docs described the wall as fenced/never-built while a full wall shipped in the live repo. The session-open protocol's "verify status against reality" step exists for exactly this; today it fired. **New corollary (LOCKED):** Code recon syncs to origin/main first — a stale local clone is never authoritative.
- **Code's lane is TEXT** · **Live DB state is Lovable's** · **Visual success criteria are Scott's** (his eyes verified every wall surface — jAIne cannot see).
- **Model routing:** Haiku (recon/greps/mechanical) · Sonnet (build + diagnosis with latitude) · **Opus (the tenant-isolation audit only).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ SHIPPED — 2026-07-16 and earlier

*(Prior session history preserved below unchanged — roster/grant-hole fix, pending→admission routing, admit-on-approval shipped & verified, the 07-15 haul, recurrence rework, Claude Code stood up, 13 real accounts confirmed, and the foundation. See git history and prior status revisions for full detail; nothing above this line was altered by the 2026-07-17 wrap except where a wall dependency touched it.)*

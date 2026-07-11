# Status
**Where the build is and what's left.** The single status board — this replaces the old build-state / burndown-tracker split, which was two docs answering one question.

Last session: **2026-07-10** (Vault dual-mode both roles · couples rail · quest audience consumer · engine bundle · anon-grant fix · doc-system migration)

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

**What's missing is not a module.** Emberhold has never met a stranger. See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

Everything else is polish. These four, in this order.

| # | Item | Blocks |
|---|---|---|
| **1** | **Stand up Claude Code** | Workstream 1, the QA harness (Layer 1), this repo's upkeep |
| **2** | **Workstream 1 — self-approval RLS + definer sweep** | Distribution. The mortal-peril item. |
| **3** | **Avatar transport** (44 chars → Lovable/Supabase) | The Guildhall, the free/paid split, the delight layer |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

---

## 🟡 PENDING VERIFY (close these first — they're cheap)

- 🟡 **Daily respawn overnight** — confirm a completed daily reappears fresh + tomorrow-dated after midnight. The one unverified half of the engine bundle. *(Physics; can't be same-day tested.)*
- 🟡 **Adult store catalog→curated flip** — give SnowDad embers past a reward cost; confirm the flip + a real instant-redeem deduction. Same component as the verified kid flip, so low risk.
- 🟡 **Phaeaz cold-account retest** — open loop since the hiatus; never confirmed.

---

## ⬜ OUTSTANDING — security & distribution (the pre-distribution bundle)

- ⬜ **Quest self-approval RLS exposure** *(CONFIRMED — blocker)*. `Update family quests` UPDATE policy gates by row with **no column-level restriction** — any claimer/assignee can write `status='approved'` / `approved_by` / points via the API. Practical risk today ≈ zero (no kid approve path, no kid session), but the core "embers mint only on approval" rule is enforced by **UI convention, not policy**.
  - The linter's `has_role(auth.uid(),'parent')` fix is **INSUFFICIENT** — kids act under the owner's session, so `auth.uid()` is always the parent.
  - Real fix: key on **acting profile** + verified-parent action + **column-level gating** (kids → claimed/in_progress only; approved/approved_by/points → verified parent only).
  - **Claude Code job.** Requires reading the parent-verification path end to end.
- ⬜ **SECURITY DEFINER sweep** — 22 definer functions, **15 tripping lint 0029**. Audit for internal caller-gating: `get_member_email`, `set_profile_pin`, `verify_profile_pin`, `member_spendable`, `approve_redemption`, `deny_redemption`, `parent_self_redeem`, `create_adult_profile`. *(`roll_missed_dailies` anon grant already revoked ✅.)*
- ⬜ **Join-code security** — no rotation, no entropy review, no approval gate. Add regenerate-code + likely admit-on-approval. *(Phaeaz's sharpest catch.)*
- ⬜ **Service worker + app-shell cache** — no functional SW (airplane-mode confirmed). Task zero of any push build; also unblocks the offline shell and makes "installable PWA" true instead of aspirational.
- ⬜ **Supabase plan + backup posture** — off the free tier (project pausing is a launch-killer); confirm point-in-time recovery.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport pass** — 44-character roster still lives **only on Scott's phone**. Files → Lovable/Supabase: naming convention, sizing/export, alpha/crop, picker slotting (incl. the 4 cool additions). *This is the largest designed-but-unshipped asset in the project.*
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Won't survive across devices or the shared-wall model, and favoriting is now load-bearing (goal-commitment) across kid store, adult store, and the rail. **Build-to-validate; fix-before-ship.**
- ⬜ **Vocabulary enforcement pass** — the string law is written (`master-spec.md` → THE VOCABULARY) but never *audited*. Lovable **reported** quest/bounty strings were already consistent and made no edits — that's a claim, not a verification. Remaining XP refs supposedly code-comments-only, also unverified. Cheap to fix, expensive to leave.
- ⬜ **Test-object cleanup** — delete/rename the `iopuyiouh` / `testy` throwaways before they fossilize into screenshots.

## ⬜ OUTSTANDING — polish (real, but not blocking)

- ⬜ **Lists: collapsible sections** — default collapsed, unchecked visible, count chip, resets to collapsed on open. Render-only.
- ⬜ **Password confirm-entry on signup** — mirror the live PIN confirm-entry. Parity fix; a mistyped signup password is a harder lockout than a PIN.
- ⬜ **Pip install tutorial** — platform-detected Add-to-Home-Screen card; suppress when installed. Zero backend.
- ⬜ **Pip help discoverability** — the Pip icon isn't read as help; needs emphasis/signposting. Ties to the above.
- ⬜ **Quick Picks polish** — expand options; convert the selector to a dropdown (match the quest-creation pattern).
- ⬜ **Reward scarcity limits** — total stock or per-user-per-period caps ("2 movie nights/month"). Vault v2 depth.
- ⬜ **Cheap Dim-tier starter reward** — the live menu floors at 25 embers, so a new kid is stuck in catalog mode until they earn 25. A fast first cash-out teaches the whole loop. Bake into starter-reward suggestions.
- ⬜ **Campaign detail view bug** — an untagged quest showing inside a campaign detail (list view correctly shows 0 of 0). Detail query missing its campaign-tag filter. Straight bug.
- ⬜ **Yearly + monthly event recurrence** — borrow the quest monthly day-of-month machinery.
- ⬜ **List suggestions in onboarding** — extend the starter-quest-library pattern to Lists. Low priority.

---

## 🅿️ PARKED (deliberate — see `parking-lot.md` for reasoning)

- 🅿️ **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + feed spine (built) + engine loop-fixes (shipped = it now has a working stage). **Fenced to post-launch** — with an open tension flagged in `north-star.md`.
- 🅿️ **#8b — admin/reporting surface** — redemption history, reward performance, weekly recap, Adventure Log, ops glance. **Deliberately parked behind the beta.** Nobody has used the app long enough to have anything worth analyzing, and six unrelated things sharing a room is a dumping ground with a disclaimer.
- 🅿️ **Kid-vs-kid impersonation** — kid PINs OFF by default (they tax the walk-up thesis). Optional lightweight picture-lock ships with the kiosk phase. **Mitigation already built:** redeemer name + face on the adult Vault approval card.
- 🅿️ **Kid-auth (username + PIN identities)** — considered and **declined**. Doesn't solve the approval hole (column-level RLS still required regardless) and taxes the walk-up model. Revisit only if the walk-up model itself changes.
- 🅿️ **Ranks as a household dial** — soften the sibling-ladder into private progress. Needs Scott's call before it's a decision.
- 🅿️ **Photo avatars** — declined raw (breaks the style floor). Near-term: roster variety. Long-term: a photo→stylized-hero pipeline *through* the floor.
- 🅿️ **Seasonal frost cosmetic set** — leftover cool-register gens + frost aesthetic. Second Guildhall tollbooth item. *Don't build the winter DLC before the base game.*
- 🅿️ **Capacitor / app-store path** — assessed viable (complexity is in Apple review, not code). Hinges on whether payment rails are ever needed. Currently: no.

---

## ✅ SHIPPED — 2026-07-10

**Engine bundle** (one prompt, three interlocking lifecycle fixes)
- ✅ **Adult quest auto-approval** — Adult submitter → `status='approved'` + `approved_by=self` written atomically; embers mint immediately, never enters the pending queue. Kid path unchanged. *Verified live.*
- 🟡 **Daily recurring board fix** — added `due_date`; board filters `due_date <= today`; `handle_quest_approval` dates the spawned instance forward (daily→tomorrow, weekly→+7, monthly→clamped); `roll_missed_dailies` RPC archives + resets yesterday's missed dailies on board mount. *Half-verified — completed daily correctly left the board; overnight respawn still pending.*
- ✅ **Edit-quest form parity** — edit modal gained the audience selector + full monthly-recurrence UI. *Shipped; UI-parity only.*

**Copy sweep**
- ✅ XP killed as user-facing vocabulary (embers only; campaign progress a bare %; `points` field name unchanged internally).
- ✅ Pre-auth possessive "hold" copy reworked to plain language (button/field labels, PipSpark, validation toasts, sign-in CTA).
- ⬜ *Quest/bounty sweep: Lovable reported already-consistent, made no edits. Unverified — see the vocabulary enforcement pass above.*

**Vault kid view — dual-mode (catalog / curated)**
- ✅ ZERO affordable → **catalog** ("The Wish Menu"): full ladder by tier, cheapest-first, all visible, favoritable. The goal-setting state.
- ✅ ONE+ affordable → **curated**: in-reach glowing set + live Redeem + "N embers left after" + next-unlock taunts with progress bars + per-tier fold. The cash-out state.
- ✅ Favorites additive, carry between modes. **Both modes verified live** (catalog at Cade=10; auto-flipped to curated at 35).

**Vault adult view — three zones**
- ✅ **Zone 1 — Awaiting Your Approval:** pending kid redemptions; card shows redeemer **name + face** (doubles as the impersonation mitigation). *Verified (empty state).*
- ✅ **Zone 2 — Your Store:** adult's own balance-anchored store, reusing the kid store component. Adults redeem instantly; never generates a Zone-1 card. *(Curated flip unwatched.)*
- ✅ **Zone 3 — Reward Menu · Manage:** edit/delete/add, no redeem buttons. *Verified.*
- ✅ Reporting **removed** from the adult Vault (data intact, relocated to the parked #8b surface).

**Couples reward rail (`reward.audience`)** — first schema change of the run
- ✅ `audience` column on rewards (`household` / `adults_only`, DEFAULT `household`); migration backfilled existing rewards. **Migration safety verified** — nothing leaked or vanished.
- ✅ Toggle in create/edit-reward form; store filters by viewer role. **Kid-side invisibility verified** — an `adults_only` reward appeared in SnowDad's store (tagged) and was completely absent from Cade's, both modes.

**Quest audience filter + badges + copy** — the long-deferred consumer, finally wired
- ✅ Visibility filter (shared rule across board + Briefing strip + count badges): kid sees `anyone` + `kids_only`; adult sees all three. *Verified — adult count 10, kid count 5, scoped bounty absent from kid side.*
- ✅ Adult-view scope badges ("Adults only" / "Kids only"; `anyone` → no badge). Not shown on the kid board.
- ✅ Copy fix — claim-eligibility states itself exactly once per card (kills the "Adults only / Available to anyone" contradiction).

**Security**
- ✅ `roll_missed_dailies` EXECUTE grant **revoked from anon**, retained for authenticated. *Verified: anon → 401; board still rolls dailies.*

**Docs / process**
- ✅ Master spec brought fully current (12 days of drift cleared: 7/03 avatar pass, 7/04 vocabulary + adult Vault, 7/10 Vault run, monetization model folded in for the first time).
- ✅ `north-star.md` created; retired the module-order roadmap (its sequencing is complete).
- ✅ **Migrated to this repo.** Undated filenames, Git owns history, one question → one doc.

---

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul **designed**: 44 characters (40 base + 4 cool-register), Feast→Hall, the generation method locked (see `playbooks/avatar-generation.md`). *Design complete; transport outstanding.*
- **2026-06-29 (eve)** — Lists v1 + sections + search, real shopping-list import, invite share + deep link (adult-only), in-app notification bell, PIN/password recovery (3 locks), collapsible avatar picker, the Avatar Style Spec.
- **2026-06-29 (AM)** — Activity feed spine, feed on the Briefing, monthly recurrence + audience flag (model+form), Parent/Family → Adult/Hold vocabulary sweep.
- **2026-06-28** — Brand marks (icon + crest), Open Bounties strip, multi-day events, FAB-becomes-submit, avatar roster v1, Pip onboarding, Combat→Forge.
- **2026-06-27** — Campaigns, Calendar, the Briefing, member identity colors, login rewrite, juice pass 1.
- **2026-06-26** — The Vault, starter quest library, fail-closed PIN, Parent Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks leaderboard, profile, quest detail, `+` FAB, brand.

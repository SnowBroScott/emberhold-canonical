# Status
**Where the build is and what's left.** The single status board — this replaces the old build-state / burndown-tracker split, which was two docs answering one question.

Last session: **2026-07-11** (daily respawn verified · vocabulary law audited + enforced · safe-area inset fix · **full roster regeneration — 48 characters**)

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

The engine's lifecycle is now **fully verified end-to-end**, not asserted. Daily respawn was the last unclosed half.

**What's missing is not a module.** Emberhold has never met a stranger. See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

Everything else is polish.

| # | Item | Blocks |
|---|---|---|
| ~~1~~ | ~~Stand up Claude Code~~ | ✅ **DONE 2026-07-11.** Native Windows install, both repos cloned. Passed its first job (the avatar slice) cleanly. |
| **1** | **Test whether Claude Code can do BUILD work** *(new — see below)* | Possibly: the entire build model |
| **2** | **Workstream 1 — self-approval RLS + definer sweep** | Distribution. The mortal-peril item. |
| **3** | **Avatar transport** — *generation ✅ · slice ✅ · upload + picker + free/paid split remain* | The Guildhall, the delight layer |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

### 🔵 NEW — the Lovable credit ceiling may no longer exist

**Lovable is now GitHub-synced** (`SnowBroScott/theemberhold`, private, 576 commits). The sync is **two-way**: Claude Code can write to the repo, push, and Lovable pulls the changes in.

**If that works, Claude Code can do build work without burning Lovable credits** — it bills against the Claude subscription instead. That would demolish the constraint behind the "batch interlocking fixes ruthlessly / roughly one meaningful prompt on free-credit days" rule that has shaped every session to date.

**Unknowns — do not assume any of these:**
- **Do migrations actually apply?** `supabase/migrations/` is in the repo, but it is unconfirmed whether Lovable *runs* a migration file pushed from outside, or whether the file just sits there while the database stays untouched. **This is the whole ballgame** — it's the difference between "Code can do frontend work" and "Code can do backend work."
- **Two-way sync means two-way conflicts.** Two agents can now write the same codebase. Prompting Lovable while Code has uncommitted work will produce divergence.
- **Lovable knows its own scaffolding; Code has to learn it.** 576 commits of conventions, patterns and routing that Lovable is native to and Code is not.

**The cheap test, already written and already queued:** the `BOUNTY POSTED` / `ROUTINE`-`MILESTONE` string bundle (see OUTSTANDING below). Pure frontend, no migration, no schema, tiny blast radius. **Give it to Code instead of Lovable.** If it lands clean, that is the most valuable thing learned this month. If it doesn't, nothing is lost — the Lovable prompt still works.

**Run this test before Workstream 1.** It's cheap, it's low-stakes, and if it passes it changes how everything after it gets built.

---

## 🟡 PENDING VERIFY

- 🟡 **Adult store catalog→curated flip** — give SnowDad embers past a reward cost; confirm the flip + a real instant-redeem deduction. Same component as the verified kid flip, so low risk.
- 🟡 **Phaeaz cold-account retest** — open loop since the hiatus; never confirmed.

---

## ⬜ OUTSTANDING — security & distribution (the pre-distribution bundle)

- ⬜ **Workstream 1 — approval-path RLS hardening** *(CONFIRMED — PRE-DISTRIBUTION BLOCKER)*. The core "embers mint only on adult approval" rule is currently enforced by UI convention rather than by policy. Confirmed finding, scoped fix, requires reading the parent-verification path end to end. **Claude Code job.** *Exploit specifics are deliberately NOT in this public repo — they live in the private security note handed into the Claude Code session.*
- ⬜ **SECURITY DEFINER sweep** — a set of definer functions flagged by the linter need an internal-caller-gating audit, bundled with Workstream 1. *(One over-grant already revoked ✅.)* *(Function-level detail held out of the public repo; in the private note.)*
- ⬜ **Join-code security** — no rotation, no entropy review, no approval gate. Add regenerate-code + likely admit-on-approval. *(Phaeaz's sharpest catch.)*
- ⬜ **Service worker + app-shell cache** — no functional SW (airplane-mode confirmed). Task zero of any push build; also unblocks the offline shell and makes "installable PWA" true instead of aspirational.
- ⬜ **Supabase plan + backup posture** — off the free tier (project pausing is a launch-killer); confirm point-in-time recovery.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport — UPLOAD + PICKER + SPLIT.** Generation ✅ and slice ✅ are both done: **48 individual 512×512 PNGs**, named `forge-01…12`, `garden-01…12`, `keep-01…12`, `hall-01…12`, on disk and in the repo. Remaining: Supabase storage upload → picker slotting → free/paid split. **These three need the app repo, which is now cloned** — so this is unblocked end to end for the first time.
- ⬜ **Feed heat vocabulary — `ROUTINE` / `MILESTONE` must die.** The activity feed invented a second tier ladder. The canonical names are Dim / Warm / Hot / Blazing, derived from the ember-value ramp at render time. Prompt written, not yet run. Bundled with:
- ⬜ **`BOUNTY POSTED` display label** — rendered on every feed row from the `bounty_posted` enum. The enum stays (it's an identifier, nobody reads the database); the rendered sentence must say `QUEST POSTED`. Prompt written, not yet run.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Won't survive across devices or the shared-wall model, and favoriting is now load-bearing (goal-commitment) across kid store, adult store, and the rail. **Build-to-validate; fix-before-ship.**
- ⬜ **Test-object cleanup** — `Daily test`, `testy`, `Testi`, `iopuyiouh`. The daily test object has served its purpose (see below) and can be retired with honor. Delete before they fossilize into screenshots.

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

- 🅿️ **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + feed spine (built) + engine loop-fixes (shipped = it now has a working stage). **Fenced to post-launch** — with an open tension flagged in `north-star.md`. *Note: the regenerated roster at 600px+ per character finally makes this visually viable; the old 170px assets did not.*
- 🅿️ **#8b — admin/reporting surface** — redemption history, reward performance, weekly recap, Adventure Log, ops glance. **Deliberately parked behind the beta.** Nobody has used the app long enough to have anything worth analyzing, and six unrelated things sharing a room is a dumping ground with a disclaimer.
- 🅿️ **Kid-vs-kid impersonation** — kid PINs OFF by default (they tax the walk-up thesis). Optional lightweight picture-lock ships with the kiosk phase. **Mitigation already built:** redeemer name + face on the adult Vault approval card.
- 🅿️ **Kid-auth (username + PIN identities)** — considered and **declined**. Doesn't solve the approval hole (column-level RLS still required regardless) and taxes the walk-up model. Revisit only if the walk-up model itself changes.
- 🅿️ **Ranks as a household dial** — soften the sibling-ladder into private progress. Needs Scott's call before it's a decision.
- 🅿️ **Photo avatars** — declined raw (breaks the style floor). Near-term: roster variety. Long-term: a photo→stylized-hero pipeline *through* the floor.
- 🅿️ **Cosmetic drop #2 — the roster surplus.** ~48 characters generated, far more than the roster needs. The overflow is a Guildhall tollbooth item that **already exists** and costs nothing further to produce. Supersedes the narrower "seasonal frost set" park. *Don't build the winter DLC before the base game.*
- 🅿️ **Capacitor / app-store path** — assessed viable (complexity is in Apple review, not code). Hinges on whether payment rails are ever needed. Currently: no.

---

## ✅ SHIPPED — 2026-07-11

**Engine — the last loop closed**
- ✅ **Daily respawn verified.** A completed daily reappeared fresh and correctly dated the following morning. This was the one unverified half of the 7/10 engine bundle. **The quest lifecycle is now verified end-to-end, not asserted.**

**Vocabulary law — audited, not claimed**
- ✅ **`OPEN BOUNTY` card chip killed.** `STATUS_LABEL.open` → `"Unclaimed"`, which cascaded correctly through every fallback (quest detail, quest-log, Briefing owner field). Redundant chip suppressed entirely inside the Open Bounties sections via a `hideStatusChip` prop.
- ✅ **A real grep audit was run and reviewed** — file-by-file, with a justification for every survivor. This is what turned the item from a claim into a closed loop. *Lovable's previous "already consistent, no changes needed" self-report was demonstrably false; the audit is now the standard, not the summary.*
- ✅ **`XP` eliminated from the install prompt.** `manifest.webmanifest` description rewritten. The last user-visible XP string in the product was the first thing a stranger read.
- ✅ **The install-prompt pitch, written:** *"Chores become quests. Quests earn embers. Embers buy real rewards. Your household, but fun."*

**Render**
- ✅ **Safe-area inset fix.** The collapsing header was rendering underneath the iOS status bar, overlapping the system clock. `pt-[max(1.5rem,env(safe-area-inset-top))]` on `AppShell` — covers every authenticated primary surface.

**Avatars — the roster regenerated from scratch**
- ✅ **48 characters. 19 sheets. 3 per sheet, ~600px each.** Four classes × twelve, every class carrying cute / core / cool / folk. Named, banked, on a real filesystem.
- ✅ **The old roster was found to be unshippable and abandoned.** Investigation revealed the four "roster boards" were **contact-sheet previews, not master assets**: ~170px per character, a bottom band Copilot had squashed, a bottom row clipped off the canvas edge, and circles packed so tightly they overlapped. *This is why avatar transport survived eight days of deferral — it was never a file-moving problem. The asset didn't exist.*
- ✅ **The avatar-generation playbook was rewritten.** Its central instruction — *generate the full roster in one run* — was wrong. See `playbooks/avatar-generation.md`; five new laws, all validated the same day.
- ✅ **Sliced by Claude Code — 48 individual 512×512 PNGs.** Cropped, vignetted to `#1A110B`, named to convention, verified against a contact sheet **that Code looked at itself.** Three tiles from `forge-cute` carried white corners (that sheet predates the black-canvas clause); Code filled and feathered them to match the other 45.

**Claude Code stood up and passed its first job**
- ✅ **Installed** (native Windows, v2.1.207). Both repos cloned locally: `emberhold-canonical` and `theemberhold`.
- ✅ **It read the docs, restated the task, and refused to start when it couldn't find the input** — rather than guessing. It also independently flagged that upload/picker/split need the app repo, which it didn't have. *That is four things this morning's tools did not do.*
- ✅ **Lovable ↔ GitHub sync connected.** `SnowBroScott/theemberhold`, private, 576 commits. **This is the first backup the app codebase has ever had** — until today it existed only inside Lovable. It also opens the build-lane question at the top of this doc.

---

## ✅ SHIPPED — 2026-07-10

**Engine bundle** (one prompt, three interlocking lifecycle fixes)
- ✅ **Adult quest auto-approval** — Adult submitter → `status='approved'` + `approved_by=self` written atomically; embers mint immediately, never enters the pending queue. Kid path unchanged. *Verified live.*
- ✅ **Daily recurring board fix** — added `due_date`; board filters `due_date <= today`; `handle_quest_approval` dates the spawned instance forward (daily→tomorrow, weekly→+7, monthly→clamped); `roll_missed_dailies` RPC archives + resets yesterday's missed dailies on board mount. *Fully verified 2026-07-11.*
- ✅ **Edit-quest form parity** — edit modal gained the audience selector + full monthly-recurrence UI. *Shipped; UI-parity only.*

**Copy sweep**
- ✅ XP killed as user-facing vocabulary (embers only; campaign progress a bare %; `points` field name unchanged internally).
- ✅ Pre-auth possessive "hold" copy reworked to plain language (button/field labels, PipSpark, validation toasts, sign-in CTA).

**Vault kid view — dual-mode (catalog / curated)**
- ✅ ZERO affordable → **catalog** ("The Wish Menu"): full ladder by tier, cheapest-first, all visible, favoritable. The goal-setting state.
- ✅ ONE+ affordable → **curated**: in-reach glowing set + live Redeem + "N embers left after" + next-unlock taunts with progress bars + per-tier fold. The cash-out state.
- ✅ Favorites additive, carry between modes. **Both modes verified live** (catalog at Cade=10; auto-flipped to curated at 35).

**Vault adult view — three zones**
- ✅ **Zone 1 — Awaiting Your Approval:** pending kid redemptions; card shows redeemer **name + face** (doubles as the impersonation mitigation). *Verified (empty state).*
- ✅ **Zone 2 — Your Store:** adult's own balance-anchored store, reusing the kid store component. Adults redeem instantly; never generates a Zone-1 card.
- ✅ **Zone 3 — Reward Menu · Manage:** edit/delete/add, no redeem buttons. *Verified.*
- ✅ Reporting **removed** from the adult Vault (data intact, relocated to the parked #8b surface).

**Couples reward rail (`reward.audience`)** — first schema change of the run
- ✅ `audience` column on rewards (`household` / `adults_only`, DEFAULT `household`); migration backfilled existing rewards. **Migration safety verified.**
- ✅ Toggle in create/edit-reward form; store filters by viewer role. **Kid-side invisibility verified.**

**Quest audience filter + badges + copy**
- ✅ Visibility filter (shared rule across board + Briefing strip + count badges): kid sees `anyone` + `kids_only`; adult sees all three. *Verified.*
- ✅ Adult-view scope badges ("Adults only" / "Kids only"; `anyone` → no badge). Not shown on the kid board.
- ✅ Copy fix — claim-eligibility states itself exactly once per card.

**Security**
- ✅ An over-broad anon EXECUTE grant on a data-mutating RPC was **revoked** (retained for authenticated). *Verified: anon → 401; board still functions.*

**Docs / process**
- ✅ Master spec brought fully current (12 days of drift cleared).
- ✅ `north-star.md` created; retired the module-order roadmap.
- ✅ **Migrated to this repo.** Undated filenames, Git owns history, one question → one doc.

---

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed: Feast→Hall, the generation method drafted. *The assets produced in this pass were later found to be contact-sheet previews rather than masters and were regenerated on 2026-07-11.*
- **2026-06-29 (eve)** — Lists v1 + sections + search, real shopping-list import, invite share + deep link (adult-only), in-app notification bell, PIN/password recovery (3 locks), collapsible avatar picker, the Avatar Style Spec.
- **2026-06-29 (AM)** — Activity feed spine, feed on the Briefing, monthly recurrence + audience flag (model+form), Parent/Family → Adult/Hold vocabulary sweep.
- **2026-06-28** — Brand marks (icon + crest), Open Bounties strip, multi-day events, FAB-becomes-submit, avatar roster v1, Pip onboarding, Combat→Forge.
- **2026-06-27** — Campaigns, Calendar, the Briefing, member identity colors, login rewrite, juice pass 1.
- **2026-06-26** — The Vault, starter quest library, fail-closed PIN, Parent Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks leaderboard, profile, quest detail, `+` FAB, brand.

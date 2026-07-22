# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-21 (evening).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-21 evening)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **Quest Log's missing `due_date` filter — by design or by omission?** Quest Log shows every quest with no `due_date` gate at all, so it displays future-dated recurring successors that the board correctly hides. This is what made the 07-21 diagnosis confusing for three rounds — two surfaces telling different true stories. Arguably correct for an oversight surface ("every quest in your hearth — oversee, approve, reassign"), arguably a leak that will confuse any adult who isn't holding the whole model in their head. **Decide deliberately.** If it stays unfiltered, consider a visual marker on not-yet-due instances rather than silence.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it.
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **Store shape — one-time founding unlock, a cosmetic catalog, or both?** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU). Living-hold themes are a *catalog* shape — potentially repeat cosmetic purchases. Different store architectures. Decide deliberately before the second SKU is built.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** Residual ambiguity: (a) a permission tier *inside* a hold, above parent — or (b) a cross-hold super-admin for tier-2 support? Different builds; (a) changes the role model, (b) is cross-tenant / P4×L8. **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** When Scott's ready: (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both one-line data changes via Lovable, no build. Selling to *new* strangers still needs Stripe (critical-path #4), decoupled on purpose.
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it (the weekly recap, parked behind beta).
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder — PARTIALLY RESOLVED 2026-07-17.** The unproven half is answered (built for own-hold, it works). What's genuinely fenced is the distribution-grade version (deployment hardening + P4×L8 on its write surface).
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **`master-spec.md` fold — the recurring-quest lifecycle + the activeness model (NEW 07-21).** Design truth changed and the spec doesn't know it. Both models are locked in `decisions.md`; they need folding into the spec's Quest section. **A short dedicated session** — read the current spec, fold, full-replace. Deliberately *not* regenerated blind at close, per protocol.
- **Monday 2026-07-27 — the recurring reappearance check.** Not a build; just open the board and look. A completed weekly is staged in the hold. See status.md PENDING VERIFY.
- **The floor — avatar render fallback (🟡 glass-verify pending).** Any unresolved/invalid pick renders a clean ember-lit placeholder, never the raw key. Verify on the glass; check the wall.
- **Founder tier-tag verification (needs a gate-on moment).** Briefly flip the gate ON → confirm exactly 32 lock / 16 open → flip back OFF. The only check that can't be done gate-off.

---

## NEXT (soon — off the critical path)

- **The timezone seam in `handle_quest_approval` (NEW 07-21).** The successor's date math uses server `CURRENT_DATE` / `date_trunc` — the same UTC-vs-local skew that broke quest creation. 07-21 fixed **creation only**. Not yet observed misbehaving, but it's the same class of defect in a second place, and the fix pattern is known. Do it before it costs another session's diagnosis.
- **Ghost successor cleanup (NEW 07-21).** "Take out the trash" carries a stale pre-migration future `due_date`. Correctly hidden; visible in Quest Log. One-row reconcile.
- **07-21 test-quest cleanup (NEW).** "testing weekly," "really testing weekly," "really REALLY testing weekly" — fold into the broader prod test-object cleanup in status.md.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall` (**confirmed live on the member editor 07-18**); grep every LOCKED decision against the codebase (the "did it land" cross-check); retire/rebuild `avatar-review.tsx` (second source of roster truth; still references the old `avatar-01…21` files).
- **Re-forge reach across the 13.** Only the logging-in member is prompted; the rest render the fallback until manually fixed. Options: prompt each member on next active session, or a hold-owner "N hearthmates need re-forging" nudge.
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Anchors invisible. Cheap conditional render. *(07-21 makes this more valuable than it looked: the anchor date is now load-bearing to how the whole lifecycle reads to a user, and it's currently invisible.)*
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model. Build phone-first; the wall inherits it (as a `wall_toggle_favorite` on-behalf write).
- **Wall Vault empty-state — two-case branch once persistent favorites ship** (affordability vs no-favorites; correct verb per cause).
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward** — the menu floors at 25; shrinks the wall's empty-Vault case; seeds the Pip "stock the Vault" screen.
- **Wall event-pill member color — dot-vs-full-tint** legibility-at-distance refinement.
- **Wall ticker speed** — tune-by-eye legibility knob, not a rebuild.
- **Multi-day calendar events (QA #7).** Events spanning day boundaries. Data-layer + rendering build; the event model likely assumes single-day.
- **Calendar alerts (QA #6).** The useful version needs scheduled push, which is fenced. A lighter in-app version might ride the existing notification layer. Decide push-vs-in-app before building.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

- **Living-hold ambient themes — monetization SKU #2 (household-level, cosmetic, delight-not-access).** Hardened 07-19 from "palette packs" into something worth charging for: each register as a *living, moving hold* rather than a recolor.
  - **The four registers as ambient scenes, differentiated by emotional register (not palette):** **Free = the hearth** — belonging; steady even glow, the baseline everything else departs from, and it stays a full tool. **Forge = heat/drive** — a *working* forge: bellows, rising embers, hotter pulse, darker charred base. **Hall = revelry** — lantern flicker, drifting warm light; warm like Free but outward and lit-up. **Garden = growth** — slow sway, drifting pollen/spores, fireflies. **Keep = restraint** — alive by being the *calmest* in the set: slow steady torchlight, barely-there banner sway. "Resolute" must read as deliberate motion, NOT static.
  - **Class is the aesthetic *source*, never the entitlement.** A hold runs mixed classes; a theme is *inspired by* a register but can never be gated behind "you must be Forge." Honors the seam rule.
  - **All themes modulate the ember-lit floor, never replace it.** Palette + motion shift; charred darks + glow-on-heat DNA stay. Protects brightness = heat = importance.
  - **Technique call — canvas particles YES, bespoke video NO.** Reference engine: Endure's `ClassParticles.tsx` (hand-rolled Canvas 2D + `requestAnimationFrame`). BUT it's a *reusable harness with bespoke per-mood content*, and Endure's other half — four pre-rendered MP4 loops per class — is a hard GPU/decode/battery cost. **Take the canvas harness; leave the video.**
  - **Kiosk-hardening is a hard requirement, not polish.** The permanent wall is a Fire HD 10-class Android tablet running *continuously for hours*. Needs `prefers-reduced-motion`, visibility-pause, frame throttling, plus a static fallback. A *scope-shaper*, not a QA step.
  - **Build order de-risks itself: Keep first.** Lowest particle count (restraint = cheapest to author + gentlest on the panel), so it doubles as the performance proving-ground. (Bias inward: pick the safe corner first, on purpose.)
  - **Pipeline adjacency:** same *shape* of problem as the composite-personalization completion-animation idea. Might be one pipeline serving both — evaluate together, not twice.
  - **Sequencing:** same gates as Founding Guildhall — behind avatar-roster validation. Nothing jumps the queue.

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions / personal profile, so every cold session starts from the best footing. Encodes the working relationship (register, lane-discipline, recon-first, the "surface the instinct then test it" loop, no-ego-stroking, tersity). Its own deliberate sober-daylight session — NOT a drive-by, NOT a project doc. *(07-21 sharpened the case: jAIne produced three consecutive wrong diagnoses by reasoning from screenshots instead of reading code, and re-flagged a non-existent bug after being corrected. Recon-first and "believe the user about their own deliberate actions" both belong in that profile.)*

- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The one identity-first word in a function-first vocabulary. The real distinction is approver vs. submitter. A display-string sweep, not a data migration. Ideally bundled with another vocab pass (e.g. the Feast→Hall sweep).

- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** See status.md for the built ledger, decisions.md for the trust model. Still fenced for the STRANGERS-grade version:
  - **Wall redemption/spend commit — future build, not yet scoped.** No pending-redemptions surface on the wall to attach to; that must be built first. Same adult-verify model. Keep OFF the wall until deliberately built.
  - **Deployment gremlins** — Safari version, PWA display-mode behavior, screen-sleep. *(Don't over-tune pixel spacing to the borrowed iPad — the permanent Fire HD 10 may render differently.)*
  - **P4×L8 on the wall's write surface** — `wall_request_redemption` + the commit-attribution class.
  - **Favorites on the wall** — deferred to per-profile persistence.

- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow is a Guildhall tollbooth item that already exists. *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable; hinges on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. Vocabulary leaves room; the permission model doesn't.
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG; KILLED.** jAIne flagged this across several turns as a lurking defect. The create form has an explicit **Assign to** selector (Open to anyone / a named member), and Scott was deliberately assigning each test quest to SnowDad to reproduce the real bug. Assignment-at-creation is a working feature, used correctly. Logged DECLINED in `decisions.md` so no future instance re-flags it. *(Root failure: jAIne inferred user intent from a stray recon line instead of believing Scott's stated deliberate action — and kept re-flagging after correction.)*
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS EVER WRITTEN.** jAIne proposed this as a decisions.md entry mid-session; the very next round proved the opposite. `due_date` is load-bearing: it is the instance's *activation date*. Replaced by the two-mechanism rule (see decisions.md).
- ~~**A client-invoked roll-forward job for weekly/monthly (extending `roll_missed_dailies`)**~~ — **SCOPED THEN CUT AS REDUNDANT.** Designed as the mechanism to make a rested weekly reappear on its anchor day. Unnecessary: `isActiveQuest` already surfaces any quest the moment `due_date <= today`, so the successor reappears on its own the first time the board loads on-or-after that date. No scheduler, no roll job, no new infrastructure. *Kept here as the reason not to rebuild it.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED before it was built** by *living-hold ambient themes* (see LATER). The differentiator that makes the SKU worth money is ambient motion per emotional register, not tint.

**2026-07-18 (evening)**
- ~~**"What happens to the thirteen existing `hero:` picks"**~~ — **RESOLVED.** Re-forge shipped. Spawned a new finding — **re-forge reach across the 13**.
- ~~**"The free/paid avatar split — arithmetic is stale"**~~ — **RESOLVED at 16/32.** The folders were the casting.
- ~~**Re-forge Pip-moment sub-question**~~ — **RESOLVED:** single-prompt with a short on-theme line, shipped.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.

**2026-07-18 (day)**
- ~~Identity-bound wall commit~~ — walked back to adult-verify.
- ~~SnowDad Vault spendable = 0 as a bug~~ — resolved as test cruft.
- ~~"Scott sets every status" doc rule + compliance checkpoint~~ — discarded, never canon.
- ~~Automated / hand-cut avatar circles~~ — superseded by fill-corners; now moot.

**2026-07-17**
- ~~Wall calendar event detail / class-color pills / realtime data / Rewards accordion~~ — shipped-or-corrected.
- Wall interaction layer — BUILT.

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, "all quiet" resolved, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, auth email promoted, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

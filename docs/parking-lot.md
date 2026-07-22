# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-22.*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-22)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Own-session vs per-member-auth — now has more riding on it than it looks (SHARPENED 07-21 late).** The parked fork about whether each hearthmate gets their own authenticated session or the household rides the owner's. **Four separate open items wait on this one call:** (1) the `adults_only` rewards audience read, (2) the `parents_only` quest details read, (3) the ignored scanner finding "adult PIN lock isn't tied to real permission checks," and (4) the ignored finding "any member can submit a redemption attributed to another member." Items 3 and 4 are currently marked **Ignored** in the Lovable dashboard — and that panel will never tell you they're conditional. **If this fork is ever decided toward per-member auth, two dismissed findings come back to life as real work.** Recorded here because the dashboard cannot hold a conditional judgment.
- **⚠️ `north-star.md` fold — jAIne asked twice, Scott hasn't called it (NEW 07-22).** The success definition and the $636 target belong in north-star: the gate ladder currently states a sequence and no destination, which is precisely why an external comparison reads in startup grammar by default. **The blocker is protocol, not content** — north-star is not one of the four close-out deliverables, and jAIne will not invent a fifth container without the call. Two options: (a) add north-star as a fifth full-replace file at close, or (b) leave it decisions-only and let north-star catch up in a dedicated pass alongside the master-spec fold. **Do not let this sit** — the whole point of the decision was to stop re-deciding the goal, and it can't do that job from `decisions.md` alone, which nobody reads cold.
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? NOW ON A CLOCK (SHARPENED 07-22).** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU, priced $25 on 07-22). **A one-time SKU funds a one-time year.** Hit the $636 target and year two needs 27 *more* new households, forever. The catalog reframe (see `decisions.md` 07-22 and the Sustaining Revenue stream in LATER) is the answer to that — a retained household becomes worth $25 + $10 + $5, which breaks break-even's dependence on perpetual acquisition. **The hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.** Not theoretical anymore; just not yet urgent.
- **Quest Log's missing `due_date` filter — by design or by omission?** Quest Log shows every quest with no `due_date` gate, so it displays future-dated recurring successors the board correctly hides. This is what made the 07-21 diagnosis confusing for three rounds — two surfaces telling different true stories. Arguably correct for an oversight surface ("every quest in your hearth"), arguably a leak. **Decide deliberately.** If it stays unfiltered, consider a visual marker on not-yet-due instances rather than silence.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it.
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** (a) a permission tier *inside* a hold, above parent — or (b) a cross-hold super-admin for tier-2 support? Different builds; (a) changes the role model, (b) is cross-tenant / P4×L8. **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both one-line data changes via Lovable, no build. Selling to *new* strangers still needs Stripe (critical-path #4), decoupled on purpose. *(07-22: grandfathering the 13 is now load-bearing to the target's honesty — the $636 goal is 27 STRANGERS, and friends-who-were-here-first shouldn't be counted toward it either way.)*
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it (the weekly recap, parked behind beta).
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder — PARTIALLY RESOLVED 2026-07-17.** The unproven half is answered (built for own-hold, it works). What's genuinely fenced is the distribution-grade version (deployment hardening + P4×L8 on its write surface). *(07-22: the wall's strategic weight went UP — it is the only semi-public Emberhold surface, and therefore the only one where a cosmetic can be seen by someone who hasn't paid. See the Sustaining Revenue stream.)*
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **Grant-revoke verification probe job (NEW 07-21 late).** Brief drafted and deferred by Scott. Proves the six revoked RPCs still answer an authenticated caller — the one thing the catalog re-read can't tell you. Full spec in `status.md` PENDING VERIFY. **The whole job hinges on one distinction: `42501` = broken grant; any app-level error = working grant.**
- **The table-grant read (NEW 07-21 late).** The last open P4×L8 slice. Function grants are provably clean; tables haven't been re-read since the 07-16 outage that dropped `authenticated` grants on fourteen of them. Cheap catalog query.
- **`master-spec.md` fold — now owes THREE things (07-21, extended 07-22).** (1) the recurring-quest lifecycle, (2) the activeness model, (3) **NEW — the Founding Guildhall price ($25) and the success definition**, both LOCKED-proposed 07-22 and both arguably spec-grade design truth. **A short dedicated session** — read the current spec, fold all three, full-replace. Deliberately *not* regenerated blind at close, per protocol. *(This is now the second doc-debt item overdue; it was already the cheapest thing on this list before 07-22 made it bigger.)*
- **Monday 2026-07-27 — the recurring reappearance check.** Not a build; just open the board and look. A completed weekly is staged in the hold.
- **The floor — avatar render fallback (🟡 glass-verify pending).** Any unresolved/invalid pick renders a clean ember-lit placeholder, never the raw key. Verify on the glass; check the wall.
- **Founder tier-tag verification (needs a gate-on moment).** Briefly flip the gate ON → confirm exactly 32 lock / 16 open → flip back OFF. The only check that can't be done gate-off.

---

## NEXT (soon — off the critical path)

- **⚠️ Calendar import — promoted from LATER; now a Gate-E thesis risk, not a backlog nicety (RE-WEIGHTED 2026-07-22).** Was filed LATER as *"never ask anyone to abandon their work calendar."* Competitive scan of **Wanmine** (Shenzhen ODM wall appliance + free companion app, ~10mo old, ~172 iOS ratings / ~1.8k Android installs) shows two-way Google/iCloud/Outlook/Yahoo sync **plus TeamSnap and GameChanger** as table stakes in the ambient-family-display category. Emberhold's calendar is a closed loop, and the calendar is one of the two backbones. **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%; two-way sync is a different and much larger project. **Decide which before Gate D**, because the landing page makes a promise either way.
- **Activity-log actor forgery (NEW 07-21 late).** `append_activity` takes `_actor_label text` from the client. Derive the actor server-side from `auth.uid()` instead. Intra-household, not a blocker — but the activity log is the *receipt* surface, and a forged actor corrupts the record an adult would use to catch anything else.
- **Ask Lovable what `sandbox_exec` is (NEW 07-21 late).** A role holding EXECUTE on every function in `public`. Pre-existing, so not drift — almost certainly platform infrastructure. Worth understanding *before* it's rediscovered mid-audit and mistaken for a breach. One question, not a project.
- **The timezone seam in `handle_quest_approval` (07-21).** The successor's date math uses server `CURRENT_DATE` / `date_trunc` — the same UTC-vs-local skew that broke quest creation. 07-21 fixed **creation only**. Same class of defect in a second place; the fix pattern is known. Do it before it costs another session's diagnosis.
- **Ghost successor cleanup (07-21).** "Take out the trash" carries a stale pre-migration future `due_date`. Correctly hidden; visible in Quest Log. One-row reconcile.
- **07-21 test-quest cleanup.** "testing weekly," "really testing weekly," "really REALLY testing weekly" — fold into the broader prod test-object cleanup in `status.md`.
- **"Forgot PIN" confirm() copy.** Claims "only the account owner" — inaccurate. UX-copy fix, surfaced by the scanner triage.
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

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream, opened 2026-07-22*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. It doesn't hunt new families — it deepens households that already adopted, so a retained household becomes worth $25 + $10 + $5 instead of $25. That reduces the strangers required even if acquisition never improves, and it's what keeps Emberhold breathing financially past year one. Reasoning in full: `decisions.md` 2026-07-22.

**The priority rule — rank by wall-visibility.** Cosmetics only aid *acquisition* where a non-customer can see them. Emberhold cosmetics live on private phones, seen by the four people who already paid. **The wall is the sole exception** — the only semi-public Emberhold artifact, seen by guests, playdate parents, and visiting family whether or not they asked. So catalog value concentrates in whatever renders on the wall.

**Consequence worth holding onto:** a wall-first catalog needs a handful of genuinely beautiful things, not volume. Four living-hold registers could be a year's entire release slate. **Never a content quota — the moment a cosmetic drop has a deadline, it stops being the reward for finishing and becomes the job.**

**Hard constraint:** all of it is downstream of day-8. A catalog amplifies retention that exists; it cannot manufacture retention that doesn't. Same gates as everything else — nothing jumps the queue.

**#1 — Living-hold ambient themes (SKU #2; household-level, cosmetic, delight-not-access).** *Highest wall-visibility: the whole surface moves.* Hardened 07-19 from "palette packs" into something worth charging for: each register as a *living, moving hold* rather than a recolor.
  - **The four registers as ambient scenes, differentiated by emotional register (not palette):** **Free = the hearth** — belonging; steady even glow, the baseline everything else departs from, and it stays a full tool. **Forge = heat/drive** — a *working* forge: bellows, rising embers, hotter pulse, darker charred base. **Hall = revelry** — lantern flicker, drifting warm light; warm like Free but outward and lit-up. **Garden = growth** — slow sway, drifting pollen/spores, fireflies. **Keep = restraint** — alive by being the *calmest* in the set: slow steady torchlight, barely-there banner sway. "Resolute" must read as deliberate motion, NOT static.
  - **Class is the aesthetic *source*, never the entitlement.** A hold runs mixed classes; a theme is *inspired by* a register but can never be gated behind "you must be Forge." Honors the seam rule.
  - **All themes modulate the ember-lit floor, never replace it.** Palette + motion shift; charred darks + glow-on-heat DNA stay. Protects brightness = heat = importance.
  - **Technique call — canvas particles YES, bespoke video NO.** Reference engine: Endure's `ClassParticles.tsx` (hand-rolled Canvas 2D + `requestAnimationFrame`). BUT it's a *reusable harness with bespoke per-mood content*, and Endure's other half — four pre-rendered MP4 loops per class — is a hard GPU/decode/battery cost. **Take the canvas harness; leave the video.**
  - **Kiosk-hardening is a hard requirement, not polish.** The permanent wall is a Fire HD 10-class Android tablet running *continuously for hours*. Needs `prefers-reduced-motion`, visibility-pause, frame throttling, plus a static fallback. A *scope-shaper*, not a QA step. *(07-22: this requirement is now also the reason this item ranks #1 — it was spec'd wall-first before anyone noticed the wall is the only surface that sells it.)*
  - **Build order de-risks itself: Keep first.** Lowest particle count (restraint = cheapest to author + gentlest on the panel), so it doubles as the performance proving-ground. (Bias inward: pick the safe corner first, on purpose.)
  - **Pipeline adjacency:** same *shape* of problem as the composite-personalization completion-animation idea. Might be one pipeline serving both — evaluate together, not twice.

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** *Dual-surface: visible on the wall AND the phone.* ~57 portraits generated for 48 slots; the overflow is a Guildhall tollbooth item **that already exists on disk with zero marginal authoring labor** — the cheapest item in the entire stream. *Don't build the winter DLC before the base game.* Related: the **seasonal frost cosmetic set** (leftover cool-register gens + frost aesthetic).

**#3 — Borders, frames, phone-only flourishes.** Real, but they sell only to people already inside. Lowest acquisition leverage in the stream; build last or never.

---

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions / personal profile, so every cold session starts from the best footing. Encodes the working relationship (register, lane-discipline, recon-first, the "surface the instinct then test it" loop, no-ego-stroking, tersity). Its own deliberate sober-daylight session — NOT a drive-by, NOT a project doc. *(07-21 sharpened the case twice: jAIne produced three consecutive wrong diagnoses by reasoning from screenshots instead of reading code — and, later the same day, the opposite pattern worked cleanly when the first move was "run the read." Recon-first, "believe the user about their own deliberate actions," and "enumerate before you triage" all belong in that profile. 07-22 adds a third: jAIne proposed a wrong strategic frame — "catalog as treadmill insurance" — and Scott corrected it in two sentences. The profile should encode that jAIne's strategic takes are inputs to be argued with, not conclusions.)*

- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The one identity-first word in a function-first vocabulary. The real distinction is approver vs. submitter. A display-string sweep, not a data migration. Ideally bundled with another vocab pass (e.g. the Feast→Hall sweep).

- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** See `status.md` for the built ledger, `decisions.md` for the trust model. Still fenced for the STRANGERS-grade version:
  - **Wall redemption/spend commit — future build, not yet scoped.** No pending-redemptions surface on the wall to attach to; that must be built first. Same adult-verify model. Keep OFF the wall until deliberately built.
  - **Deployment gremlins** — Safari version, PWA display-mode behavior, screen-sleep. *(Don't over-tune pixel spacing to the borrowed iPad — the permanent Fire HD 10 may render differently.)*
  - **P4×L8 on the wall's write surface** — `wall_request_redemption` grants tightened 07-21 late; the commit-attribution class remains.
  - **Favorites on the wall** — deferred to per-profile persistence.

- **Seed distribution channels — pick one or two, never all (RE-SURFACED 07-22, originally scoped 07-01).** Recovered from the 07-01 monetization session because Scott remembered it existed and the docs had lost it. **The highest-leverage lever is not a channel at all — it's the invite/deep-link loop.** Family apps grow household-by-household, and every household is 3–5 people who each know other parents; that compounds without Scott grinding, and it's a *build* task, which is the part he enjoys. Polish the share card, the landing-on-invite experience, the first-run for an invited household. *Then*, on top of it:
  - **One earnest post in r/daddit, r/Mommit, or r/homeschool** — "I built this for my own family, here's what it does," with screenshots. Those communities like an honest indie parent-builder. **An afternoon, not a campaign.**
  - **Product Hunt + PWA directories** (Appscope and friends) — a launch-day spike plus a permanent backlink. Low effort, one-time.
  - **Habitica's audience already exists.** Proven appetite for gamified-life stuff. Don't compete with them; be findable by people who already want this.
  - **The homeschool angle (🔵 VALIDATED 07-10, from Phaeaz's network — see `status.md`).** Kids home most of the day means more household coordination surface and higher daily-habit intensity, which is exactly the moat. **This one came from outside Scott's head, which makes it worth more than the ones that came from inside it.**
  - **Honest read, carried forward unchanged:** visibility for a niche PWA with no store presence is *slow*. It will not go viral. **Don't become a content marketer** — if "get visibility" turns into a part-time SEO job it won't get done and Scott will hate it. *(07-22: the $636 / 27-household target makes this materially less daunting than it reads. 27 strangers over twelve months is roughly one every other week.)*

- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. Vocabulary leaves room; the permission model doesn't.

---

## KILLED / SUPERSEDED

**2026-07-22**
- ~~**"Match Wanmine's feature breadth — meal planner, home inventory, photo stream, message board"**~~ — **REJECTED ON SIGHT.** Four modules with no game in them. This is precisely the jack-of-all-trades creep the membrane exists to prevent, and Wanmine having them is not a gap — it's them selling a screen. Their business gets paid at checkout and has **zero incentive to solve day 8**; Emberhold's business is *only* day 8. Captured so a future session doesn't rediscover the feature list and feel behind.
- ~~**"Capacitor / app-store path — assessed viable; hinges on whether payment rails are ever needed"**~~ — **MOVED TO A DECIDED CALL. DECLINED 2026-07-22** with two named reopen triggers (PWA push proving load-bearing at Gate E; or an appliance play after Gate E returns retention driven by the wall). Full reasoning in `decisions.md`. Kept here because the *indefinite deferral* framing is what let it resurface as an open question three weeks running.
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED BY EVIDENCE, not by opinion.** Wanmine: ~172 iOS ratings and ~1.8k Android installs in ten months, with a hardware product behind it. Their store listing is a support channel for people who already bought the screen. **Nobody discovers a free lifestyle app by browsing.** Logged separately from the decision above because this is the *belief* that kept regenerating the question.

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED AS A VERIFICATION METHOD.** A static scanner re-runs rules; it cannot confirm that a grant landed or that a live path still works. It would have re-emitted lint 0029 (permanent noise) and told us nothing about 0028's actual state. The correct verification was the catalog re-read plus an authenticated probe. *A scan is a sweep, never a proof.* Kept here so the reflex doesn't come back.
- ~~**"Fix the SECURITY DEFINER findings with the panel's 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.** Five of the ten findings are load-bearing behavior (the join flow, the wall's propose tier, the shared-session model). An auto-fix on `wall_request_redemption` would have broken a shipped feature to close a finding that isn't one.

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG; KILLED.** The create form has an explicit **Assign to** selector, and Scott was deliberately assigning each test quest. Logged DECLINED in `decisions.md`. *(Root failure: jAIne inferred user intent from a stray recon line instead of believing Scott's stated deliberate action — and kept re-flagging after correction.)*
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS EVER WRITTEN.** The very next round proved the opposite. `due_date` is load-bearing: it is the instance's *activation date*. Replaced by the two-mechanism rule.
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** `isActiveQuest` already surfaces any quest the moment `due_date <= today`. No scheduler, no roll job. *Kept here as the reason not to rebuild it.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED before it was built** by *living-hold ambient themes* (now #1 in the Sustaining Revenue stream).

**2026-07-18 (evening)**
- ~~**"What happens to the thirteen existing `hero:` picks"**~~ — **RESOLVED.** Re-forge shipped. Spawned **re-forge reach across the 13**.
- ~~**"The free/paid avatar split — arithmetic is stale"**~~ — **RESOLVED at 16/32.**
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

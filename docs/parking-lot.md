# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-29 (late).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-29)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** A holdmate added without an avatar renders as a silhouette-in-a-frame. **jAIne's lean: do NOT auto-default and do NOT guess "first of each class"** — the roster is the one place a hearthmate says "this is me," and stamping a portrait picks their identity for them. The empty state is already the honest state; what's missing is a **nudge** — make the empty seat tappable → routes to the picker. **The one exception is the wall**, where a silhouette is a dead spot on an ambient display — that's a wall-render fallback (the "floor" item), not an onboarding default. **Raised 2026-07-29, jAIne recommended, Scott never ratified.** Now folded into `master-spec.md` as jAIne's recommendation; the decision and the tappable-affordance build are both still owed.
- **WHERE DOES THE REST-TIMER ALERT FIRE — the garage screen, or the pocket?** The always-on screen removes the iOS background-suspension problem. But the timer is the most personal and most interruptive element, and with two people on different intervals a shared screen beeping for one is noise for the other. **jAIne's lean: the screen owns the SESSION; phones own the TIMER.** Taste call in a room jAIne can't see. **Scott's.**
- **In the agenda lane, does the rest timer SHARE the cell with load/weight/reps or REPLACE it?** Scott's shower-design sketch has it replace. **jAIne flagged the objection, never ratified:** rest is exactly when you most want to see what you're about to lift, and swapping it out means tapping back, wet-handed, at the beep. **Unresolved. Visual call, Scott's.**
- **Does the LLM plan BOTH sessions in one call, or one call per person?** One call is what makes staggered openers and contention-aware generation possible. **The contention feature effectively requires one call — confirm that's intended before it becomes an implicit constraint.**
- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived from `auth.uid()` and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller** — a parent approving a kid's quest correctly shows the kid's name. The column is doing two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels and let the feed compose. **(b) is jAIne's lean — and it is now the same shape as the marker's `profile_id` fix, which strengthens the case for the pattern.** Design call, not a bug fix.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** **The curriculum decision settles the permission question** — creation stays ungated. **What is unsettled is which surface is wrong.** Same class as `approved_by`/`decided_by`.
- **THE FORGE'S SHAPE — Option A vs Option B, inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** **As of 07-28, A has a real shape** — prescription-first, pre-session gate, catalog-backed substitution, contention resolution, reason string, log, rest timer, display mode. **Now written into `master-spec.md` Part II as design truth.** **The bar is a USER TEST: Scott and May stop opening Fitbod.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence of what was trained. No fungible currency — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.
  - **What B owes:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? (3) does a collection survive the calibration check?
  - **The standing risk:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. **A one-time SKU funds a one-time year.** **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Quest Log's missing `due_date` filter — by design or by omission?** **Load-bearing as a diagnostic surface.** **Decide deliberately.**
- **Module navigation.** Seven tabs is already one past mobile comfort. **It is not "add another tab."** **Forge's contained-route posture defers this entirely.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.** *(Second-best argument, new: this session shipped a live creator-signup break to production and caught it on a manual walk. A staging pass would have caught it before publish.)*
- **⚠️ Backend ownership + data backup.** Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 REDEMPTION APPROVAL MUST RECORD THE PIN-VERIFIED ADULT — AND ITS DEADLINE IS BREACHED.** `approve_redemption` hardcodes `decided_by = auth.uid()`. The rule was "true before the arrival gate goes live"; the gate went live 2026-07-29. **The `/first-run/adult/pin` screen names redemption explicitly, so this is a live product making a specific claim it does not honor.** Shipped on a deliberate call — invisible in a single-adult hold, and the joiner flow was worth more than an exact promise — but the window has closed. **First prompt of the next build session. Do NOT fix the string instead.**
- **🔴 THE MARKER'S READ AND WRITE, AS ONE CHANGE.** `FirstRunGate` reads the account holder; `mark_first_run_complete()` writes `WHERE id = auth.uid()`. A kid sub-profile's row id is not any `auth.uid()`, so a kid's write **no-ops silently and returns success**. ⚠️ **The two bugs cancel — fixing the read alone gives an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, Lovable lane. **Blocks the kid joiner flow on shared devices, which is the majority kid path.**
- **🔴 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Two derivations of role now exist** — `profiles.role` in `FirstRunGate`, `user_roles` in `useMyProfile()`. **The `isActiveQuest` divergence pattern, second occurrence.**
- **Unapproved weekly/monthly quests never roll forward.** Two stranded on the live board. **Now written into the spec as a defect rather than as correct behavior. Likely also the STALE chip answer — verify that first, it may be one fix.**
- **Recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** The anchor arithmetic *discards* the user's chosen day and the UI never says so.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone. **Test whether this is a Claude Code drop-and-point job first.** *(Cheaper now: this session created two throwaway test holds that could serve as the seed — check before building a third.)*
- **Grant-revoke verification probe job.** Drafted, deferred four times.
- **The floor — avatar render fallback.** **Check the wall.** *(Also the resolution path for the empty-roster-seat open decision.)*
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**
- **Prod test-object cleanup — deserves its own session.** **Now includes this session's two glass-check holds.**

---

## NEXT (soon — off the critical path)

### Onboarding, phase three

- **Walk the kid joiner flow with an actual kid.** Three beats to the adult's five, built and published, never exercised. **Lean is not automatically wrong**, but nobody has watched one go through it. **Blocked in practice on the shared-device path until the marker read/write pair lands.**
- **Add the install-tutorial screen to the joiner flow.** The creator flow has it; the eight non-creator screens don't. **No longer blocked** — the joiner flow is live. Same screen, copy re-checked.
- **The `/first-run/*` copy deserves a second read now that it's live.**
- **The stacked-Pip-voice line on the first setup screen.** The yellow "Ember's still warm — let's finish lighting your hearth" now sits under a header that also speaks ("Who's tending the hold?"). Two Pip voices stacked. Cosmetic, low priority.
- **`points` is surfacing as a user-facing noun.** `/first-run/adult/approving` reads *"the points on that quest become real, spendable embers"* — a string-law violation. A quest is *worth* embers; approval *mints* them. Saying points *become* embers invents a second unit. **Queued for the free Haiku sweep.**
- **A creator who bails mid-onboarding gets the joiner tour on return, not their resumed setup.** Degraded, not broken, and strictly better than the pre-marker empty board. **Logged, not fixed.**
- **`member_admitted` renders as `"Mom · Leo"`.** One case in two switches.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Toolchain

- **⚠️ `routeTree.gen.ts` was hand-edited and there is version drift underneath it.** **Probably self-healing**, but local `npm run dev`/`build` is a trap until someone reconciles the versions.
- **Claude Code's "sync before reading" rule needs a forcing function.** Its clone was 16 commits stale this session and only discovered it at push time. **Cheapest fix is a line in every Code brief: report the `origin/main` hash BEFORE reading, not just after pushing.** Costs nothing and makes staleness visible at the top of the job.

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for V1.** **Option A's shape now lives in `master-spec.md` Part II as design truth — this section holds build order only.***

**THE BUILD ORDER IS NOT AMBIGUOUS.**

1. **🔴 THE EXERCISE CATALOG — first, and it's a Make job.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Four features ride it:** injury substitution, rack-taken swaps, resequencing, and the pre-session gate's relevance check. **Generate offline → Scott reviews → ship as data.** Can live in Supabase rather than as a static asset, so the substitution map is tunable without a redeploy.
2. **🔴 ENGINE REP-COMPENSATION — parallel, zero credits, Claude Code.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo. **It cannot express "175 instead of 185, so give me 8 instead of 5,"** which is now half the differentiator. Pure TypeScript, ten tests, zero Supabase imports. **Needs a validity floor:** below some deviation the app must say *"that's a different exercise now."*
3. **The pre-session gate's forward input path.** RPE autoregulation is backward-looking; nothing currently accepts a declared constraint *before* prescribing. **Scope with #2.**
4. **Slice one: log a set and get the reason string.** Schema + gated route + themed empty state, then the loop. **Out of slice one:** program generation, cardio UI, bands, LLM anything, activity-feed writes.

**Open build-time details not yet in the spec:**
- **Equipment needs `exclusive` vs `shareable`.** One field, needed before contention resolution means anything.
- **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Participation is a per-member flag, not a role check** — when it's needed. **Do not build it now.**

### Option B ideas — parked until A has been used for a month

- **The tool wall** · **component-level chronology** · **item aging** · **the shared-barbell plate delta** *(arguably belongs in Option A, and 07-28's contention work makes that argument stronger)* · **the pure/mixed trap** — mixed and pure must be different-beautiful, never ranked.

### Endure — the separate product

- **🅿️ Parked, not killed, and the block is platform.** **There is no HealthKit web API**, and the PWA posture is LOCKED. **Principles ported; systems did not.**
- **Endure is the app that does it DIFFERENT. Forge is the app that does it the SAME, for less money.**
- **Reopen trigger: the same two that reopen Capacitor.**

### Everything else

- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is *built*. **The trigger has not fired.**
- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide before Gate D.**
- **⚠️ COPPA is sharper than the spec used to say — and the spec now says so.** A minor joining by code has an email, a password, and an auth row. **The shield the spec leaned on doesn't cover the door strangers will actually use.** Gate C.
- **⚠️ `flock.js` analytics — know the surface before Gate C.** A tracker (self-proxied `/~api/analytics`, likely Lovable-injected) fires in the app `<head>`. **It has to be named truthfully in the Gate C privacy policy.**
- **The service worker.** ⚠️ Its Forge-driven priority bump is retired, **and it is NOT a prerequisite for the install tutorial.** It still matters for the offline themed shell (a Gate B stranger-proofing item) and **deserves its own careful pass** — a botched offline cache is a silent-stale-content risk.
- **Ask Lovable what `sandbox_exec` is.** One question, not a project.
- **Ghost successor cleanup.** **"Forgot PIN" confirm() copy.** **`decisions.md` header nit** — the prose "Status tiers" line omits SUPERSEDED.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall`; **grep for user-facing `points` → the string law says embers**; grep every LOCKED decision against the codebase; retire/rebuild `avatar-review.tsx`.
- **Re-forge reach across the 13.**
- **Vault favorites → real per-profile persistence.** Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward — DE-PRIORITIZED.**
- **Wall event-pill member color.** **Wall ticker speed.** **Multi-day calendar events.** **Calendar alerts** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

### Forge at stranger scale — what does NOT come along

*Now folded into `master-spec.md` Part II. Kept here only as a pointer.* Injury-prescription liability posture · the commercial-gym equipment model · the returning connectivity clause · display mode as a semi-public surface · the reactivating free/paid split · cold, dusty, humid garage hardware.

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Emberhold cosmetics live on private phones. **The wall is the sole exception.** *(A garage Forge screen is a second room-visible surface — it widens the argument if Forge ever ships to strangers.)*

**A sharper test than "delight vs. function": is this thing a CHOICE or a MEMORY?** Expression is choice — always free, always reversible. A **mark** is memory — earned through something that actually happened, and what is paid for is the *ritual of permanence*.

**Hard constraint:** all of it is downstream of day-8.

**#1 — Living-hold ambient themes (SKU #2).** Each register as a *living, moving hold*. **Free = the hearth.** **Forge = heat/drive · Hall = revelry · Garden = growth · Keep = restraint** (alive by being the calmest).
  - **Register is the aesthetic *source*, never the entitlement.**
  - **All themes modulate the ember-lit floor, never replace it.**
  - **Canvas particles YES, bespoke video NO.**
  - **Kiosk-hardening is a hard requirement, not polish.**
  - **Build order de-risks itself: Keep first.**

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots.

**#3 — Borders, frames, phone-only flourishes.** Build last or never.

---

- **The timezone nudge — a signal, never an inference.** If the household's stored zone has disagreed with every adult's device for ~30 consecutive days, Pip mentions it once. **A human confirms; the system never acts.**
- **The "how Scott & jAIne work" collaboration profile.** **07-29 (late) supplied three more entries, and they cluster.** jAIne fenced the exact file containing the fix she needed (DO-NOT-BUILD said "don't touch `FirstRunGate`," meaning the profile bug; the route exemption lives in the same file). She wrote a dots prompt that would have collapsed three deliberately different registry lengths into one shared constant — the sweep instinct applied to something that wasn't a class. And she asserted "nothing is shipped" from a preview screenshot, which only shows current preview state, not publish history. **Prior entries:** quoted a LOCKED rationale as an argument instead of re-deriving it; asserted a route guard's behavior from an agent's prose summary; asserted the marker keyed to the switched-into profile after reading the helper and inferring the consumer; proposed a production stub-flip that would have ambushed thirteen households; invented a wife's name; asserted a repo-capture leak in a working habit; claimed nothing had ever written `'training'`; called four rows a guilt pile without reading `archived`; prescribed a glass check the frontend could not perform. **The profile should encode: fetch before producing · decompose before you promote · read the CONSUMER, not the helper · follow a proposed change downstream before calling it reversible · state a hypothesis as a hypothesis · never invent a fact about Scott's life · re-derive a LOCKED rule's reason before using it as an argument · check the deployed commit before diagnosing a missing feature · right-size the artifact to the task · DON'T FENCE THE FILE THAT HOLDS THE FIX · SWEEP THE CLASS ONLY WHEN IT IS ONE · A PREVIEW SCREENSHOT SHOWS PREVIEW STATE, NOT PUBLISH HISTORY.** *Scott has overruled jAIne on the naming call, the module-merge call, the currency call, the layer-collapse call, the screen-3 call, the assign-only call, the plate-inventory call, the silent-timezone call, the connectivity call, and the revert call (he didn't revert; he was right — the fix landed inside the window and a revert would have added rollback commits for Code to rebase onto).*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration. *(Satisfied on net-new `/first-run/*` strings at zero cost.)*
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is the invite/deep-link loop.** **Don't become a content marketer.**
- **#8b — admin/reporting surface** — parked behind beta. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline.** Never a bare photo in the circle.
- **"Spin for a quest."** Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-29 (late)**
- ~~**"`master-spec.md` owes seven folds"**~~ — **CLOSED.** Regenerated against a full read of the current doc plus every `decisions.md` entry from 07-26 forward. Six non-mechanical calls surfaced for objection before the file was produced; none overruled.
- ~~**"Answer the RLS question before scoping the marker — can a member UPDATE their own `profiles` row?"**~~ — **DISSOLVED, THEN ANSWERED ANYWAY.** Routing through a SECURITY DEFINER RPC works regardless, so the recon was skipped and cost nothing. The answer turned up free in an old migration: **yes, they can** — and it wouldn't have mattered, because that policy carries the same `id = auth.uid()` ceiling as the RPC. **The cheap path was a dead end.**
- ~~**"`mark_first_run_complete()` might mark every profile under the caller's `auth.uid()`"**~~ — **STRUCTURALLY IMPOSSIBLE.** `profiles.id` **is** the auth user id; there is no separate `user_id` column to fan out across. **The inverse is the real problem** — a kid sub-profile's row is unreachable by that predicate, so the write no-ops. Moved to NOW.
- ~~**"Fix `FirstRunGate`'s account-holder read as its own bundled item"**~~ — **KILLED AS A STANDALONE.** Fixing the read alone produces an infinite flow loop, because the write is wrong in the same direction and the two currently cancel. **Read and write ship together or not at all.**
- ~~**"Make every first-run screen derive its segment count from a single source of truth"**~~ — **CAUGHT BEFORE IT RAN.** Three step registries have three deliberately different lengths (creator 6, adult joiner 5, kid joiner 3). A shared constant would have broken a joiner trail that was already correct. Prompt rewritten to derive per-registry.
- ~~**"Nothing is shipped — that's a preview screenshot"**~~ — **FALSE, AND jAIne ASSERTED IT.** A preview screenshot shows current preview state, not publish history. The install tutorial was already live.
- ~~**"Revert the publish"**~~ — **DECLINED BY SCOTT, AND HE WAS RIGHT.** The exemption fix landed inside the same window; reverting would have added rollback commits for Claude Code to rebase onto and produced a messier history for no safety gain, given effectively zero cold traffic.

**2026-07-29 (early)**
- ~~**"The install tutorial needs a service worker to work"**~~ — **UNBUNDLED.** Install and standalone launch come from the manifest + iOS meta tags. The SW is offline resilience, a separate Gate B job.
- ~~**"The install screen fell out of the sequence / the detection is firing"**~~ — **NEITHER; STALE LOCAL BITS.**
- ~~**"The install tutorial is a nine-illustration, four-branch state machine"**~~ — **RIGHT-SIZED TO ONE SCREEN.**
- ~~**"Frame the share-sheet screenshot with a painted Pip illustration"**~~ — **REPLACED BY THE REAL SCREENSHOT** in a charred forge-window border.
- ~~**"'Now show them' and 'the rest of your hold' are both redundant tour screens, cut both"**~~ — **SPLIT.** "Now show them" cut; "the rest of your hold" KEPT as the one breadth-signal.
- ~~**"Auto-default an avatar for a member who skipped the picker"**~~ — **LEANED AGAINST, UNRATIFIED.**

**2026-07-28**
- ~~**"Forge v1 is a logger with programming bolted on"**~~ — **INVERTED BY USER RESEARCH.** **v1 is a prescription engine with a log attached.**
- ~~**"The rest timer is polish"**~~ — **FALSE.** Named top-three value. **The in-session habit hook.**
- ~~**"Injury severity has to be inferred or modeled"**~~ — **KILLED BY ONE TAP.**
- ~~**"Injury flags need a duration or a decay timer"**~~ — **KILLED BY SCOTT.** **The training split IS the expiry mechanism.**
- ~~**"Readiness and injury are one 'how are you feeling' dial"**~~ — **KILLED.**
- ~~**"Make can be the runtime generation engine"**~~ — **KILLED AS RUNTIME, KEPT AS FACTORY.**
- ~~**"The client-engine rule exists because a garage has unreliable connectivity"**~~ — **CORRECTED BY SCOTT.** **The rule survives on latency and sunk cost.** *(Folded into the spec 2026-07-29.)*
- ~~**"Split view for two concurrent sessions on one screen"**~~ — **KILLED BY THE AVATAR-LANE INSIGHT (Scott's).**
- ~~**"Minute-by-minute session scheduling"**~~ — **KILLED.** Bias at generation, don't schedule.
- ~~**"A live countdown / session-duration nag"**~~ — **DECLINED BY SCOTT.**
- ~~**"Forge display mode is a toggle of the existing wall display mode"**~~ — **CORRECTED, THEN RECONCILED** as idle-defaults-to-wall precedence.
- ~~**"The wall's never-mints-never-edits rule constrains a Forge screen"**~~ — **DOES NOT BIND.**
- ~~**"The pre-session gate lives on a phone, in the kitchen"**~~ — **SUPERSEDED WITHIN THE SAME SESSION.**
- ~~**"Equipment presence detection"**~~ — **CUT ON SIGHT.**

**2026-07-27 (late)**
- ~~**"The joiner flow is a variant of the existing creator flow, re-routed"**~~ — **KILLED BY PERMISSIONS.**
- ~~**"The joiner flow is a genuinely separate artifact"**~~ — **ALSO KILLED.** **Same shell, second step registry.**
- ~~**"Fix the routing fork in `auth.tsx`"**~~ — **KILLED.** **The fork is not the seam.**
- ~~**"Hook the flow to the pending→active transition"**~~ — **KILLED AS AN EVENT.** **A state check on arrival.**
- ~~**"'Replay the intro' folds into the joiner flow"**~~ — **KILLED ON MODALITY.**
- ~~**"The kid joiner is a rare bird because kids have no auth identity"**~~ — **FALSIFIED BY SCOTT.** **Two real doors.**
- ~~**"Preserve `requested_role` so the app can tell a demoted joiner"**~~ — **DECLINED.**
- ~~**"Flip the read stub to `false` in production to walk the flow"**~~ — **WITHDRAWN BY jAIne BEFORE IT RAN.**
- ~~**"The `/first-run/*` screens are unverifiable until the column lands"**~~ — **FALSE, AND jAIne SAID IT TWICE.**
- ~~**"The marker keys to the switched-into profile"**~~ — **FALSE of `FirstRunGate`.**
- ~~**"A derived gate can replace the completion marker"**~~ — **KILLED.** **No way to remember a skip.**
- ~~**"Put the completion marker in localStorage"**~~ — **KILLED.**

**2026-07-27 (early)**
- ~~**"Activity-log actor forgery is the top open security item"**~~ — **DOWNGRADED.**
- ~~**"Derive `actor_label` from `auth.uid()`"**~~ — **KILLED AS A REGRESSION.** *(Now an explicit DO-NOT in the spec.)*
- ~~**"Wipe `families.timezone` and let it repopulate"**~~ — **IMPOSSIBLE.**
- ~~**"Use NTP to solve the timezone problem"**~~ — **WRONG TOOL.**
- ~~**"Store a UTC offset instead of a zone name"**~~ — **KILLED.**
- ~~**"Phaeaz's hold was mis-detected at signup"**~~ — **FALSE.**
- ~~**"Silent auto-correct of the household timezone"**~~ — **OVERRULED BY SCOTT.**
- ~~**"Per-member timezones"**~~ — **REJECTED.**
- ~~**"Build the timezone edit path as a settings form"**~~ — **SUPERSEDED BY THE SET-ONCE HEAL.**
- ~~**"Constrained plate-count search for prescription snapping"**~~ — **CUT, then found ALREADY BUILT.**
- ~~**"Build Forge's equipment model around Scott's gym"**~~ — **OVERRULED.**
- ~~**"Forge must be a full Fitbod replacement"**~~ — **SUPERSEDED** by a user test.
- ~~**"Rebuild Forge as a standalone tool"**~~ — **KILLED THE SAME NIGHT.**
- ~~**"Forge dilutes Emberhold"**~~ — **STRUCTURALLY IMPOSSIBLE.**
- ~~**"Cardio can't be included because a PWA can't validate it"**~~ — **CONFLATED TWO THINGS.**
- ~~**"Session lane can be inferred from location"**~~ — **FALSIFIED.**
- ~~**"Rest-period design leaks out of the repo"**~~ — **FALSE.**

**2026-07-26**
- ~~**"The Clean Toys rows are a fourth activeness surface"**~~ — **KILLED BY DATA.** *(Note now removed from the spec.)*
- ~~**"The three stale dailies are a guilt pile"**~~ — **KILLED THE SAME WAY.**
- ~~**"Monthly recurrence may clamp and drift"**~~ — **STRUCTURALLY IMPOSSIBLE.**
- ~~**"The routing fork determines whether a new household sees onboarding at all"**~~ — **FALSE.**
- ~~**"Nothing has ever written `'training'` to `enabled_modules`"**~~ — **FALSE.** *(Correction now in the spec: the `'training'`→`'fitness'` change is a data migration, not a string edit.)*
- ~~**Screen 3's feature overview as a FOOTER**~~ — **OVERRULED BY SCOTT.**
- ~~**"The first quest must be assigned to a named hearthmate"**~~ — **OVERRULED.**
- ~~**Scripted screenshot capture**~~ — **DECLINED.** · ~~**Lovable generating the screen-3 imagery**~~ — **DECLINED.**
- ~~**Module intent as a first-run screen**~~ — **CUT, not killed.** · ~~**Back navigation in the setup flow**~~ — **DECLINED for now.**

**2026-07-25 (late)**
- ~~**The four registers as four functional layers**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED.**
- ~~**"Sparks" as a Forge-local currency**~~ — **MOOT.** · ~~**Four charters with three stubbed**~~ — **CUT.**
- ~~**The vocabulary noun "layer"**~~ — **RETIRED.** · ~~**Renaming `enabled_modules` → `enabled_layers`**~~ — **REVERSED.**
- ~~**Porting Endure's systems into Emberhold**~~ — **CUT ON A PLATFORM WALL.**

**2026-07-25 (early)**
- ~~**Emberforge as a standalone sibling app**~~ — **KILLED.** The diagnosis was PROPORTION.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.**
- ~~**Reverting the Lovable project**~~ — **REJECTED ON EVIDENCE.**
- ~~**Loot rolls, salvage, and a Resources currency**~~ — **CUT.** · ~~**The Hold / clans / blueprints**~~ — **CUT.**

**2026-07-22**
- ~~**"Match Wanmine's feature breadth"**~~ — **REJECTED ON SIGHT.**
- ~~**"Capacitor / app-store path"**~~ — **DECLINED** with two named reopen triggers.
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED.**

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED.** *A scan is a sweep, never a proof.*
- ~~**"Fix the SECURITY DEFINER findings with the 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.**

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG.**
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS WRITTEN.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** ⚠️ *Blind to unapproved quests. Two weeklies are stranded because of it.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED** by living-hold ambient themes.

**2026-07-18 (evening)**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

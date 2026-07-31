# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-30 (late).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-30 late)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 WHAT DOES THE LEDGER ACTUALLY SHOW?** The Slate's shape is decided; the Ledger's is not. It holds "anything finished" — but that is a filter, not a design. Open: is it a flat reverse-chronological list, grouped by member, grouped by month? Does it carry ember values? Is it a *record* (adult-facing, auditable) or a *scrapbook* (kid-facing, the thing you're proud of)? **jAIne's lean: scrapbook first, because the Adventure Log is fenced and this is the cheapest version of that feeling.** Not raised with Scott yet.
- **🔴 DOES THE SLATE REPLACE A TAB, OR ADD ONE?** Quest Log's current entry point is unexamined and navigation is already at seven tabs — one past mobile comfort, per the module-navigation item below. Slate + Ledger is two surfaces where there was one. **Must be answered inside the Slate build prompt, not after.**
- **What is the SLATE's empty state?** A brand-new hold has no standing duties. The Slate is the "does this already exist" surface, and on day one the answer is "nothing does." **Pip's line here is a real onboarding moment and nobody has written it.**
- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** **jAIne's lean: do NOT auto-default and do NOT guess "first of each class"** — the roster is the one place a hearthmate says "this is me." The empty state is already the honest state; what's missing is a **nudge** — make the empty seat tappable → routes to the picker. **The one exception is the wall**, where a silhouette is a dead spot on an ambient display. **Raised 2026-07-29, jAIne recommended, Scott never ratified.**
- **WHERE DOES THE REST-TIMER ALERT FIRE — the garage screen, or the pocket?** **jAIne's lean: the screen owns the SESSION; phones own the TIMER.** Taste call in a room jAIne can't see. **Scott's.**
- **In the agenda lane, does the rest timer SHARE the cell with load/weight/reps or REPLACE it?** Scott's sketch has it replace. **jAIne flagged the objection, never ratified.** Visual call, Scott's.
- **Does the LLM plan BOTH sessions in one call, or one call per person?** **The contention feature effectively requires one call — confirm that's intended before it becomes an implicit constraint.**
- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller.** The column is doing two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels. **(b) is jAIne's lean — same shape as the marker's `profile_id` fix.** Design call, not a bug fix.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** **The curriculum decision settles the permission question** — creation stays ungated. **What is unsettled is which surface is wrong.**
- **THE FORGE'S SHAPE — Option A vs Option B, inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** Prescription-first, pre-session gate, catalog-backed substitution, contention resolution, reason string, log, rest timer, display mode. **The bar is a USER TEST: Scott and May stop opening Fitbod.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence. No fungible currency.
  - **What B owes:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? (3) does a collection survive the calibration check?
  - **The standing risk:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. **A one-time SKU funds a one-time year.** **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Module navigation.** Seven tabs is already one past mobile comfort. **It is not "add another tab."** ⚠️ **Now sharper: the Slate and the Ledger are two surfaces replacing one.** **Forge's contained-route posture defers the module half entirely; the Slate/Ledger half does not defer.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.**
- **⚠️ Backend ownership + data backup.** Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 THE ROW-PRIMITIVE RECON — read-only, zero credits, Sonnet. FIRST.** Four hypotheses briefed to disprove (badge-is-culprit · N independent implementations · title-always-loses · length-is-not-the-variable), plus a recurring-quest census by cadence. **The census sizes the Slate's collapse behavior** — Scott says ten monthlies, the docs say four, and nobody has counted.
- **🔴 THE SHARED ROW PRIMITIVE — Claude Code, zero credits, auto-accept ON, isolated push.** Shrink-to-fit → wrap → cap at two lines; badge below the title. ⚠️ **A shared primitive touches Vault, wall and board at once — Scott eyeballs three surfaces, not one.** **Do not stack it with other changes; the whole point is a one-commit revert.**
- **🔴 THE SLATE + THE LEDGER — Lovable lane. The next credit spend.** Carries the roll-forward migration AND the Bounty sweep in one prompt. **Do not un-bundle the sweep** — it is the rename's only landing check, and an unlanded rename is how Feast→Hall sat dead for ten days.
- **🔴 THE MARKER'S READ AND WRITE, AS ONE CHANGE.** ⚠️ **The two bugs cancel — fixing the read alone gives an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, Lovable lane. **Blocks the kid joiner flow on shared devices.**
- **🔴 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **⚠️ WATCH THE BOARD ON AUGUST 1.** Every monthly successor is dated 2026-08-01 and they all land the same morning. **This is the free, natural test of both the monthly post-fix and the board-clutter concern that drove the Slate.** Two days out. Costs nothing but looking.
- **Recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** ⚠️ **Promoted.** The anchor arithmetic discards the user's chosen day and the UI never says so — and the Slate surfaces "next due" on every row, so a lying chip becomes a lying Slate.
- **Two derivations of role now exist** — `profiles.role` in `FirstRunGate`, `user_roles` in `useMyProfile()`. **The `isActiveQuest` divergence pattern, second occurrence.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by the roll-forward fix — verify before building anything separate.**
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone. *(Cheaper now: prior sessions created throwaway test holds that could serve as the seed.)*
- **Grant-revoke verification probe job.** Drafted, deferred five times.
- **The floor — avatar render fallback.** **Check the wall.**
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **The Vault-page approval path, post-migration.** Single adult, approve a pending kid redemption from the Vault page. Cheap.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**
- **Prod test-object cleanup — deserves its own session.**

---

## NEXT (soon — off the critical path)

### The rename's tail

- **`master-spec.md` full regeneration.** ⚠️ **Sequenced AFTER the Bounty sweep lands, not before.** The rename inverts the string law, renames the core object noun across 669 lines, replaces the Quest Log surface entry with two, and amends the recurring lifecycle. **Regenerating canon to say "Bounty" while the app says "Quest" would make the doc diverge from the product** — the exact disease the repo exists to prevent. Until then, the 07-30 LOCKED decisions govern over the spec's older lines.
- **`north-star.md` string inheritance.** Two lines pick up the rename when the sweep lands: the membrane line ("the game lives in quests only") and the one-line description. **Cosmetic inheritance — do not treat it as a north-star change or regenerate the doc for it.**
- **The install-prompt description is a LOCKED decision containing the word "quests" twice.** *"Chores become quests. Quests earn embers."* **It is superseded by the rename and needs its own entry when the sweep runs** — not a silent edit.
- **The Haiku vocabulary sweep gains a third target:** `Feast`→`Hall`, user-facing `points`→embers, and now `Quest`→`Bounty`. ⚠️ **Run the Quest sweep with the Slate prompt, not in the Haiku batch** — it is far too wide to be a mechanical grep-and-swap, and the 07-11 precedent is that a self-reported clean sweep shipped OPEN BOUNTY on six cards an hour later.

### Onboarding, phase three

- **Walk the kid joiner flow with an actual kid.** Three beats to the adult's five, built and published, never exercised. **Blocked in practice on the shared-device path until the marker read/write pair lands.**
- **Add the install-tutorial screen to the joiner flow.** Same screen, copy re-checked.
- **The `/first-run/*` copy deserves a second read now that it's live** — and again after the rename.
- **The stacked-Pip-voice line on the first setup screen.** Two Pip voices stacked. Cosmetic, low priority.
- **`points` is surfacing as a user-facing noun.** `/first-run/adult/approving` reads *"the points on that quest become real, spendable embers"* — a string-law violation twice over now. **Queued for the free Haiku sweep.**
- **A creator who bails mid-onboarding gets the joiner tour on return, not their resumed setup.** Degraded, not broken. **Logged, not fixed.**
- **`member_admitted` renders as `"Mom · Leo"`.** One case in two switches.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Toolchain

- **⚠️ `routeTree.gen.ts` was hand-edited and there is version drift underneath it.** **Probably self-healing**, but local `npm run dev`/`build` is a trap until someone reconciles the versions.
- **Claude Code's "sync before reading" rule needs a forcing function.** **Cheapest fix is a line in every Code brief: report the `origin/main` hash BEFORE reading, not just after pushing.** *(Applied in the row-primitive recon brief.)*

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for V1.** Option A's shape lives in `master-spec.md` Part II — this section holds build order only.*

**THE BUILD ORDER IS NOT AMBIGUOUS.**

1. **🔴 THE EXERCISE CATALOG — first, and it's a Make job.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Four features ride it.** **Generate offline → Scott reviews → ship as data.** Can live in Supabase so the substitution map is tunable without a redeploy.
2. **🔴 ENGINE REP-COMPENSATION — parallel, zero credits, Claude Code.** `NextLoad` returns `{loadLb, reason, holdsAfter}`. **It cannot express "175 instead of 185, so give me 8 instead of 5."** Pure TypeScript, ten tests, zero Supabase imports. **Needs a validity floor.**
3. **The pre-session gate's forward input path.** **Scope with #2.**
4. **Slice one: log a set and get the reason string.** **Out of slice one:** program generation, cardio UI, bands, LLM anything, activity-feed writes.

**Open build-time details not yet in the spec:**
- **Equipment needs `exclusive` vs `shareable`.**
- **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Participation is a per-member flag, not a role check** — when it's needed. **Do not build it now.**

### Option B ideas — parked until A has been used for a month

- **The tool wall** · **component-level chronology** · **item aging** · **the shared-barbell plate delta** · **the pure/mixed trap** — mixed and pure must be different-beautiful, never ranked.

### Endure — the separate product

- **🅿️ Parked, not killed, and the block is platform.** **There is no HealthKit web API**, and the PWA posture is LOCKED. **Principles ported; systems did not.**
- **Endure is the app that does it DIFFERENT. Forge is the app that does it the SAME, for less money.**
- **Reopen trigger: the same two that reopen Capacitor.**

### Everything else

- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is *built*. **The trigger has not fired.**
- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide before Gate D.**
- **⚠️ COPPA is sharper than the spec used to say.** A minor joining by code has an email, a password, and an auth row. Gate C.
- **⚠️ `flock.js` analytics — know the surface before Gate C.** A tracker fires in the app `<head>`. **It has to be named truthfully in the Gate C privacy policy.**
- **The service worker.** Offline themed shell, Gate B. **Deserves its own careful pass** — a botched offline cache is a silent-stale-content risk.
- **Ask Lovable what `sandbox_exec` is.** One question, not a project.
- **Ghost successor cleanup.** **"Forgot PIN" confirm() copy.** **`decisions.md` header nit** — the prose "Status tiers" line omits SUPERSEDED.
- **Re-forge reach across the 13.**
- **Vault favorites → real per-profile persistence.** Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward — DE-PRIORITIZED.**
- **Board clutter under roll-forward — reuse the Lists collapsible sections (`fb6aa99`), do not build a new pattern.** A live monthly is real work someone should claim; don't hide it, group it.
- **Wall event-pill member color.** **Wall ticker speed.** **Multi-day calendar events.** **Calendar alerts** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

### Forge at stranger scale — what does NOT come along

*Folded into `master-spec.md` Part II. Kept here only as a pointer.* Injury-prescription liability posture · the commercial-gym equipment model · the returning connectivity clause · display mode as a semi-public surface · the reactivating free/paid split · cold, dusty, humid garage hardware.

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Emberhold cosmetics live on private phones. **The wall is the sole exception.**

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
- **The "how Scott & jAIne work" collaboration profile.** **07-30 (late) supplied four more, and they cluster differently from the prior batch — these are FRAMING failures, not factual ones.** jAIne answered a findability question with migration plumbing, and Scott then argued against the plumbing as though it were the design. She proposed a title `maxLength` that would have made users pay for a layout defect. She asserted a truncation culprit from a three-for-three correlation *in a photograph* and stated it as a finding. And she excluded embers from the Slate on membrane grounds while reasoning only about reading, missing the editing case entirely — Scott supplied it in one sentence. **Prior entries:** fenced the exact file containing the fix she needed; wrote a prompt that would have collapsed three deliberately different registry lengths; asserted "nothing is shipped" from a preview screenshot; quoted a LOCKED rationale instead of re-deriving it; asserted a route guard's behavior from an agent's prose summary; proposed a production stub-flip that would have ambushed thirteen households; invented a wife's name; claimed nothing had ever written `'training'`; called four rows a guilt pile without reading `archived`; prescribed a glass check the frontend could not perform. **The profile should encode: fetch before producing · decompose before you promote · read the CONSUMER, not the helper · state a hypothesis as a hypothesis · never invent a fact about Scott's life · re-derive a LOCKED rule's reason before using it as an argument · right-size the artifact to the task · DON'T FENCE THE FILE THAT HOLDS THE FIX · SWEEP THE CLASS ONLY WHEN IT IS ONE · A PREVIEW SCREENSHOT SHOWS PREVIEW STATE, NOT PUBLISH HISTORY · ANSWER THE QUESTION THAT WAS ASKED · A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS · FIX THE CONTAINER, NOT THE CONTENT.** *Scott has overruled jAIne on the naming call, the module-merge call, the currency call, the layer-collapse call, the screen-3 call, the assign-only call, the plate-inventory call, the silent-timezone call, the connectivity call, the revert call, the embers-on-the-Slate call, the mid-row-clip call, and the user-authored-content call.*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration. ⚠️ **Consider folding into the Bounty sweep** — same lane, same file set, same review pass. *(Satisfied on net-new `/first-run/*` strings at zero cost.)*
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is the invite/deep-link loop.** **Don't become a content marketer.**
- **#8b — admin/reporting surface** — parked behind beta. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality. ⚠️ **The Ledger may be a cheaper first version of this feeling.**
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline.** Never a bare photo in the circle.
- **"Spin for a quest."** Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-30 (late)**
- ~~**"Quest is the universal object term"**~~ — **SUPERSEDED BY BOUNTY.** The 07-04 rule and its 07-11 enforcement both fall. **The rule was not violated for three weeks; it was incoherent, and the repeated re-litigation was the symptom.**
- ~~**"Bounty survives as a proper noun in exactly one place"**~~ — **THE CARVE-OUT IS THE DEFECT.** A rule permitting two words for one object on the same screen is a rule a designer can hold and a user cannot.
- ~~**Quest Log**~~ — **REPLACED by the Slate and the Ledger.** Its "no `due_date` filter — by design or omission?" open question **DISSOLVED rather than being answered**: the real question was always what the surface is *for*.
- ~~**"Marquee / scroll the truncated titles on the wall"**~~ — **DECLINED.** May's request, and the honest read of it is that motion was requested because truncation felt broken. Six simultaneously scrolling titles on an ambient display is a slot machine.
- ~~**"Cap title length at creation"**~~ — **DECLINED, AND IT WAS jAIne'S.** Killed by the photos within one exchange: the truncating titles were 14 and 20 characters. **The proposal would have made users pay for a layout defect.**
- ~~**"The wall's mid-row clip is a defect"**~~ — **DECLINED BY SCOTT** from daily live use. He has never once read it as broken. **jAIne raised it unprompted while already holding two accepted findings — the tell for finding-inflation.**
- ~~**"`Video Game` is a Title Case violation of the string law"**~~ — **CATEGORY ERROR, CORRECTED BY SCOTT.** The string law governs *app copy*. A reward name a human typed is household content. **The inverse of the 07-11 identifiers-vs-copy finding, and a future audit will absolutely try to "fix" it.**
- ~~**"Embers don't belong on the Slate — it becomes a second board"**~~ — **OVERRULED BY SCOTT, WITH THE BETTER ARGUMENT.** jAIne was reasoning about *reading*; Scott about *editing*. Correcting a mis-valued bounty currently costs a delete-and-recreate, which destroys history and breaks the succession chain.
- ~~**"Ship the row primitive as its own Lovable push"**~~ — **RE-LANED, NOT KILLED.** Scott's stated requirement was easy revert; **Code serves that better than Lovable** — one commit, `git revert`, zero credits, and it's frontend-only so it's Code's lane under the one-writer rule anyway.
- ~~**"Spawn a fresh successor for missed recurring instances"**~~ — **KILLED AS THE GUILT-PILE SHAPE.** Same row moving forward = no pile. New row spawning = the pile. **This was never proposed as such, but it is the intuitive implementation and it must stay killed in writing.**

**2026-07-30 (early)**
- ~~**"Redemption `decided_by` is the urgent 🔴 because the deadline is breached"**~~ — **THE FIX WAS RIGHT AND THE REASON WAS WRONG.** Nothing reads the column. It shipped because it cost ~1 credit and zero taps. **Scott caught this, not recon.**
- ~~**"`approved_by`'s behavior is correct and `decided_by` moves to match it"**~~ — **KILLED BY D4.** Quest approval never validates that the written value names an adult. **A recon briefed to disprove killed a line in canon.**
- ~~**"The marker fix and the redemption fix are coupled"**~~ — **KILLED BY D5.** They share a pattern, not a dependency.
- ~~**"Use `has_role()` to validate the approver"**~~ — **KILLED BY LOVABLE.** It reads `user_roles.user_id`, which only exists for auth-backed profiles — **it would have rejected the exact co-parent the feature exists to record.**

**2026-07-29 (late)**
- ~~**"`master-spec.md` owes seven folds"**~~ — **CLOSED.** *(Note: a new and much larger debt opened 07-30 late.)*
- ~~**"Answer the RLS question before scoping the marker"**~~ — **DISSOLVED, THEN ANSWERED ANYWAY.**
- ~~**"`mark_first_run_complete()` might mark every profile under the caller's `auth.uid()`"**~~ — **STRUCTURALLY IMPOSSIBLE.** **The inverse is the real problem.**
- ~~**"Fix `FirstRunGate`'s account-holder read as its own bundled item"**~~ — **KILLED AS A STANDALONE.** **Read and write ship together or not at all.**
- ~~**"Make every first-run screen derive its segment count from a single source of truth"**~~ — **CAUGHT BEFORE IT RAN.**
- ~~**"Nothing is shipped — that's a preview screenshot"**~~ — **FALSE, AND jAIne ASSERTED IT.**
- ~~**"Revert the publish"**~~ — **DECLINED BY SCOTT, AND HE WAS RIGHT.**

**2026-07-29 (early)**
- ~~**"The install tutorial needs a service worker to work"**~~ — **UNBUNDLED.**
- ~~**"The install screen fell out of the sequence"**~~ — **NEITHER; STALE LOCAL BITS.**
- ~~**"The install tutorial is a nine-illustration, four-branch state machine"**~~ — **RIGHT-SIZED TO ONE SCREEN.**
- ~~**"Frame the share-sheet screenshot with a painted Pip illustration"**~~ — **REPLACED BY THE REAL SCREENSHOT.**
- ~~**"Cut both tour screens"**~~ — **SPLIT.** "Now show them" cut; "the rest of your hold" KEPT.
- ~~**"Auto-default an avatar for a member who skipped the picker"**~~ — **LEANED AGAINST, UNRATIFIED.**

**2026-07-28**
- ~~**"Forge v1 is a logger with programming bolted on"**~~ — **INVERTED BY USER RESEARCH.**
- ~~**"The rest timer is polish"**~~ — **FALSE.** **The in-session habit hook.**
- ~~**"Injury severity has to be inferred"**~~ — **KILLED BY ONE TAP.** · ~~**"Injury flags need a decay timer"**~~ — **KILLED BY SCOTT.**
- ~~**"Readiness and injury are one dial"**~~ — **KILLED.** · ~~**"Make can be the runtime engine"**~~ — **KEPT AS FACTORY.**
- ~~**"The client-engine rule exists because a garage has unreliable connectivity"**~~ — **CORRECTED BY SCOTT.**
- ~~**"Split view for two concurrent sessions"**~~ — **KILLED BY THE AVATAR-LANE INSIGHT (Scott's).**
- ~~**"Minute-by-minute session scheduling"**~~ · ~~**"A live countdown / duration nag"**~~ · ~~**"Equipment presence detection"**~~ — **ALL KILLED.**
- ~~**"Forge display mode is a toggle of the wall"**~~ — **RECONCILED** as idle-defaults-to-wall precedence.
- ~~**"The wall's never-mints rule constrains a Forge screen"**~~ — **DOES NOT BIND.**

**2026-07-27 (late)**
- ~~**"The joiner flow is a variant of the creator flow"**~~ — **KILLED BY PERMISSIONS.** · ~~**"...a separate artifact"**~~ — **ALSO KILLED.** **Same shell, second step registry.**
- ~~**"Fix the routing fork in `auth.tsx`"**~~ — **KILLED.** · ~~**"Hook the flow to the pending→active transition"**~~ — **A state check on arrival.**
- ~~**"The kid joiner is a rare bird"**~~ — **FALSIFIED BY SCOTT.** **Two real doors.**
- ~~**"Flip the read stub to `false` in production"**~~ — **WITHDRAWN BY jAIne BEFORE IT RAN.**
- ~~**"The `/first-run/*` screens are unverifiable until the column lands"**~~ — **FALSE, AND jAIne SAID IT TWICE.**
- ~~**"A derived gate can replace the completion marker"**~~ · ~~**"Put the marker in localStorage"**~~ — **BOTH KILLED.**

**2026-07-27 (early)**
- ~~**"Activity-log actor forgery is the top open security item"**~~ — **DOWNGRADED.**
- ~~**"Derive `actor_label` from `auth.uid()`"**~~ — **KILLED AS A REGRESSION.**
- ~~**"Silent auto-correct of the household timezone"**~~ — **OVERRULED BY SCOTT.** · ~~**Per-member timezones**~~ — **REJECTED.**
- ~~**"Forge must be a full Fitbod replacement"**~~ — **SUPERSEDED** by a user test.
- ~~**"Forge dilutes Emberhold"**~~ — **STRUCTURALLY IMPOSSIBLE.**

**2026-07-26**
- ~~**"The Clean Toys rows are a fourth activeness surface"**~~ · ~~**"The three stale dailies are a guilt pile"**~~ — **BOTH KILLED BY DATA.**
- ~~**"Monthly recurrence may clamp and drift"**~~ — **STRUCTURALLY IMPOSSIBLE.**
- ~~**"Nothing has ever written `'training'` to `enabled_modules`"**~~ — **FALSE.**
- ~~**Screen 3's feature overview as a FOOTER**~~ · ~~**"The first quest must be assigned"**~~ — **BOTH OVERRULED BY SCOTT.**

**2026-07-25**
- ~~**The four registers as four functional layers**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED.**
- ~~**"Sparks" as a Forge-local currency**~~ · ~~**The vocabulary noun "layer"**~~ · ~~**Renaming `enabled_modules`**~~ — **ALL RETIRED.**
- ~~**Emberforge as a standalone sibling app**~~ — **KILLED.** The diagnosis was PROPORTION.

**2026-07-22 / 07-21 / 07-19 / 07-18**
- ~~**"Capacitor / app-store path"**~~ — **DECLINED** with two named reopen triggers.
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED.** *A scan is a sweep, never a proof.*
- ~~**"The Quick Add favorite chip silently auto-assigns"**~~ — **NEVER A REAL BUG.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** ⚠️ *Blind to unapproved quests. **Now formally reversed by the 07-30 roll-forward decision.***
- ~~**Theme packs as palette swaps**~~ — **SUPERSEDED** by living-hold ambient themes.
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

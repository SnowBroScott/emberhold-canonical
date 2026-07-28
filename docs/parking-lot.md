# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-27 (late).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-27 late)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived from `auth.uid()` and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller** — a parent approving a kid's quest correctly shows the kid's name. The column is doing two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels and let the feed compose. **(b) is jAIne's lean.** Design call, not a bug fix. **Now partially unblocked** — the `approved_by`/`decided_by` half of this bundle is resolved, so this can be decided alone.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** Recon found `campaign.$id.tsx` gates "New tagged quest" on `isParent` while `BottomBar`'s FAB and `QuickAddTray` carry no role condition. **The curriculum decision settles the permission question** — creation stays ungated, absence not gating. **What is unsettled is which surface is wrong.** Removing the campaign gate is consistent with the decision; adding a FAB gate contradicts it. Same class as `approved_by`/`decided_by`: two surfaces disagreeing about one rule.
- **THE FORGE'S SHAPE — Option A vs Option B, inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** Set logging, automatic programming, rest timing, progressive adjustment, the reason string. **The bar is a USER TEST: Scott and May stop opening Fitbod.** ~$192/yr of cost avoidance against a $636/yr target.
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence of what was trained. No fungible currency — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.
  - **What B owes before it can be written as truth:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? (3) does a collection survive the calibration check?
  - **The standing risk:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. **A one-time SKU funds a one-time year.** Hit $636 and year two needs 27 *more* new households, forever. **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Quest Log's missing `due_date` filter — by design or by omission?** **Load-bearing as a diagnostic surface.** Arguably correct for an oversight surface, arguably a leak. **Decide deliberately.**
- **Module navigation.** Seven tabs is already one past mobile comfort. **It is not "add another tab."** **Forge's contained-route posture defers this entirely.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it. *(Best argument: eight tables and two functions were created in production by accident.)*
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather; (2) flip `system_flags.founder_gate_enabled`. ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 `master-spec.md` — FIVE folds owed, dedicated pass, ALREADY SCHEDULED.** Creator onboarding flow · signup posture · write-once timezone and its heal · Part II's rescoping to household-only · **the non-creator first run, the marker seam, and the arrival-state rule.** Plus five corrections found on the read: the `actor_label` derive-from-`auth.uid()` prescription is a killed regression, the "fourth activeness surface" note is dead, the `set_enabled_modules` "nothing has ever written that value" claim is false, the Onboarding section still calls the empty-board gap the top structural problem, and the COPPA shield is false for a join-by-code minor. **jAIne has read the current version.**
- **🔴 The first-run completion marker — the Lovable half of a finished frontend.** A `profiles` column plus a write path. ⚠️ **Scope the RLS question first: can a member UPDATE their own `profiles` row?** If not it's a column *plus* an RPC. **Three things ship together or the regression returns** — the constant, the read body, the write body.
- **🔴 `FirstRunGate` reads the account holder, not the switched-into profile.** Ships with the marker or the kid flow never fires for the kids it was built for.
- **🔴 Redemption approval moves to match the PIN screen's promise.** The string is already live and says the PIN ties a redemption to whoever signed off. Make it true.
- **🔴 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed.
- **Two derivations of role now exist** — `profiles.role` in `FirstRunGate`, `user_roles` in `useMyProfile()`. Agreeing today because one writer sets both. **The `isActiveQuest` pattern, second occurrence.** Unify behind one helper.
- **Unapproved weekly/monthly quests never roll forward.** Two stranded on the live board. **Likely also the STALE chip answer — verify that first, it may be one fix.**
- **Recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** The anchor arithmetic *discards* the user's chosen day and the UI never says so.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone, drop three PNGs into `src/assets/`. **Test whether this is a Claude Code drop-and-point job before spending a Lovable credit.**
- **Grant-revoke verification probe job.** Drafted, deferred three times. `42501` = broken grant; any app-level error = working grant.
- **The floor — avatar render fallback.** **Check the wall.**
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**
- **Prod test-object cleanup — deserves its own session.**

---

## NEXT (soon — off the critical path)

### Onboarding, phase three

- **The `/first-run/*` copy deserves a second read once it's live.** The kid array is three beats to the adult's five. **Lean is not automatically wrong** — a kid needs less — but nobody has watched an actual kid go through it, and that is the audience the day-8 thesis rides on.
- **`member_admitted` renders as `"Mom · Leo"`.** A milestone verb with no case in `hearth-log.tsx` or `NotificationBell.tsx`, falling to `default`. **One case in two switches.**
- **The optimistic `true` will flash.** When the marker is real, a joiner sees the board for a frame before being pulled into the walkthrough. Cosmetic.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week. Wrong reward for being early.

### Toolchain

- **⚠️ `routeTree.gen.ts` was hand-edited and there is version drift underneath it.** The locally-installed `@tanstack/router-plugin` produces stricter codegen than whatever generated the committed file — running it surfaces ~40 pre-existing `search`-param errors across untouched files. **Probably self-healing**, since the route files exist and Lovable's codegen will pick them up. **But local `npm run dev`/`build` is a trap until someone reconciles the versions**, and "one writer at a time" turns out to have a dependency-tree cousin nobody had named.

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for V1.** The tripwire is SUBSTITUTION.*

- **Build from scratch against `families` / `profiles` — not by resurrection.** The rebuild reuses **no schema** and **one artifact**: `src/lib/progression.ts` + its ten tests. **A module never mints a person.**
- **Containment posture:** route at `/forge`, gated on `families.enabled_modules` containing `'fitness'`, **no nav entry.** **The flip is two things** — the module gate (built) and a doorway in navigation (unsolved, deferred).
- **🔴 THE ENGINE GAP:** `progression.ts` has **no progression axis except load.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo, not stubbed. **It cannot express "175 instead of 185, so give me 8 instead of 5"** — the named differentiator. **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits, desktop-bound.**
- **What the engine already does:** takes inventory as a parameter · bounded subset-sum over finite plate counts · `"timed"` equipment type returning `null` · pure stateless e1RM. **Better shape than assumed.**
- **Slice one: log a dumbbell set and get the reason string.** Schema (~five tables) + gated route + themed empty state, then the loop. **Out of slice one:** program generation, cardio UI, bands, plate-loaded snapping, LLM anything, activity-feed writes.
- **Offline is slice 1.5.** Schema must not preclude it; the build ships online-first.
- **Cardio is IN and it is prescribed, not autoregulated.** A session must hold **entries of two shapes** and must allow **mixed sessions** from day one.
- **Bands are out of v1.** Once a non-load entry shape exists they become a rank plus reps. Door unwelded, not opened.
- **The differentiator is explainability, and its unbuilt half is rep compensation.** Needs a validity floor: below some deviation the app must say *"that's a different exercise now."*
- **`prescribed` and `actual` are two fields, never one.** Autoregulation reads actual, always.
- **Progression logic never lives in Postgres.**
- **The garage wall is an *input* device, not a display.** **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Multi-member concurrent sessions are a design requirement.** **Rest timer is derived, never stored.** **Attribution, not concurrency, is the real risk.**
- **No PIN on set logging.**
- **Participation is a per-member flag, not a role check** — when it's needed. **Do not build it now.**

### Option B ideas — parked until A has been used for a month

- **The tool wall** · **component-level chronology** · **item aging** · **the shared-barbell plate delta** *(arguably belongs in Option A)* · **the pure/mixed trap** — mixed and pure must be different-beautiful, never ranked.

### Endure — the separate product

- **🅿️ Parked, not killed, and the block is platform.** **There is no HealthKit web API**, and the PWA posture is LOCKED. **Principles ported; systems did not.**
- **Endure is the app that does it DIFFERENT. Forge is the app that does it the SAME, for less money.**
- **Reopen trigger: the same two that reopen Capacitor.**

### Everything else

- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is *built*. **The trigger has not fired.**
- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** Wanmine ships two-way sync as table stakes. **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide before Gate D.**
- **⚠️ COPPA is sharper than the spec says.** The spec claims kids-have-no-auth-identity as the COPPA advantage. **A minor joining by code has an email, a password, and an auth row.** At 13 that's fine. A 10-year-old walks the identical path and nothing asks. Gate C already names COPPA — **what's new is that the shield the spec leans on doesn't cover the door.**
- **The service worker's priority may rise with Forge.** A garage has unreliable connectivity.
- **Ask Lovable what `sandbox_exec` is.** One question, not a project.
- **Ghost successor cleanup.** **"Forgot PIN" confirm() copy.** **`decisions.md` header nit** — the prose "Status tiers" line omits SUPERSEDED.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall`; grep every LOCKED decision against the codebase; retire/rebuild `avatar-review.tsx`.
- **Re-forge reach across the 13.**
- **Vault favorites → real per-profile persistence.** Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward — DE-PRIORITIZED.**
- **Wall event-pill member color.** **Wall ticker speed.** **Multi-day calendar events.** **Calendar alerts** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

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

- **The timezone nudge — a signal, never an inference.** If the household's stored zone has disagreed with every adult's device for ~30 consecutive days, Pip mentions it once. **A human confirms; the system never acts.** **Only if the set-once heal plus the reset button prove insufficient.**
- **The "how Scott & jAIne work" collaboration profile.** **07-27 late supplied two more entries and they share the shape of all the others.** jAIne asserted a route guard's behavior from an agent's prose summary rather than the file, then asserted the marker keyed to the switched-into profile after reading the helper and inferring the consumer — true in `first-run.ts`, false in `FirstRunGate`. **Also proposed a production stub-flip that would have ambushed all thirteen households with an unskippable walkthrough, and described it as "one line, reversible" without following it downstream.** Prior entries: invented a wife's name; asserted a repo-capture leak in a working habit; claimed nothing had ever written `'training'`; called four rows a guilt pile without reading `archived`; prescribed a glass check the frontend could not perform. **The profile should encode: fetch before producing · decompose before you promote · read the CONSUMER, not the helper · follow a proposed change downstream before calling it reversible · state a hypothesis as a hypothesis · never invent a fact about Scott's life.** *Scott has overruled jAIne on the naming call, the module-merge call, the currency call, the layer-collapse call, the screen-3 call, the assign-only call, the plate-inventory call, and the silent-timezone call.*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration. **The `/first-run/*` copy was written to this standard from the start and cost nothing extra** — proof the sweep is affordable on net-new strings even if the retrofit isn't scheduled.
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is the invite/deep-link loop.** Then: one earnest post in r/daddit / r/Mommit / r/homeschool; Product Hunt + PWA directories; **the homeschool angle (🔵 VALIDATED).** **Don't become a content marketer.**
- **#8b — admin/reporting surface** — parked behind beta. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline.** Never a bare photo in the circle.
- **"Spin for a quest."** Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-27 (late)**
- ~~**"The joiner flow is a variant of the existing creator flow, re-routed"**~~ — **KILLED BY PERMISSIONS, NOT BY THE SHELL.** The shell is genuinely neutral: zero creator-only writes, no reference to role or session state. But **two of the three writing screens are parent-gated** — `add-family` and `stock-vault`. A kid walked through the existing flow eats an RLS denial behind a friendly mascot, which is the exact failure the curriculum decision exists to prevent, arriving through a different door.
- ~~**"The joiner flow is a genuinely separate artifact"**~~ — **ALSO KILLED.** Duplicating a container that carries no assumptions buys nothing. The answer was neither: **same shell, second step registry.**
- ~~**"Fix the routing fork in `auth.tsx` so `join` goes to a joiner flow"**~~ — **KILLED.** A joiner is still `status='pending'` at that point and lands on `PendingWaitingScreen`. Firing a walkthrough there teaches someone to claim a quest and then parks them for a day. **The fork is not the seam.**
- ~~**"Hook the flow to the pending→active transition"**~~ — **KILLED AS AN EVENT.** Admission usually happens with the app closed; the joiner returns the next day to a plain `/board` load and the event has long since passed. **It must be a state check on arrival.**
- ~~**"'Replay the intro' folds into the joiner flow — one artifact, three consumers"**~~ — **KILLED ON MODALITY, and jAIne killed her own proposal.** The flow is a full-screen routed takeover; replay is a dismissible sheet reached from a help affordance. Folding makes replay far too heavy or the first run far too light. The audiences have opposite knowledge states and opposite exits. **The content is shareable and extracting a shared source for two consumers — one of which is four hardcoded blocks that already work — is premature.** They shared a workshop question, not an artifact.
- ~~**"The kid joiner is a rare bird because kids have no auth identity"**~~ — **FALSIFIED BY SCOTT IN ONE SENTENCE.** A 13-year-old with their own phone and a join code is normal traffic for the top half of the kid range, and the join form has an adult/kid selector they will use. **Two real doors, not one door plus an edge case.**
- ~~**"Preserve `requested_role` so the app can tell a demoted joiner what happened"**~~ — **DECLINED.** `admit_pending_member` nulls it in the same UPDATE that sets the confirmed role, so the evidence is gone by design. **Rebuilding it to power a notification is the wrong fix:** an app telling a teenager "you were downgraded" is worse than a parent telling them "kid for now, we'll revisit." **The demotion is a household conversation, not a system message.**
- ~~**"Flip the read stub to `false` in production to walk the flow"**~~ — **WITHDRAWN BY jAIne BEFORE IT RAN, AND IT WAS A BAD PROPOSAL.** A flat `false` syncs to Lovable and hands every member of all thirteen households an unskippable adult walkthrough on their next board load — unskippable because the write no-ops. **Described as "one line, reversible" without following it downstream.** The correct fix was the stub's semantics, not a backdoor.
- ~~**"The `/first-run/*` screens are unverifiable until the column lands"**~~ — **FALSE, AND jAIne SAID IT TWICE.** The `369164e` guard fix made the routes reachable by URL. **The copy was always readable — it's text, not a visual criterion — and jAIne filed the checkable half under "can't check."**
- ~~**"The marker keys to the switched-into profile, so the profile-switch door is handled by construction"**~~ — **FALSE.** True of `redirectIfFirstRunDone`, which uses `getActiveMemberIdSync()`. **False of `FirstRunGate`, which is the thing that actually fires the flow** — it reads `profile` from `.eq("id", data.user.id)`, the account holder. jAIne read the helper and inferred the consumer.
- ~~**"A derived gate can replace the completion marker"**~~ — **KILLED, and the reason generalizes.** `FirstQuestDoorway` solves activation with a live quest count and no flag at all, which is a genuinely good pattern. It doesn't transfer: **a derived gate has no way to remember a skip.** Skip navigates to `/board`, `/board` re-evaluates, the flow re-fires forever. The doorway gets away with it because it's a dismissible banner; a takeover flow cannot.
- ~~**"Put the completion marker in localStorage"**~~ — **KILLED.** On a shared device one profile's marker suppresses every profile's flow. The Vault-favorites debt, repeating.

**2026-07-27 (early)**
- ~~**"Activity-log actor forgery is the top open security item"**~~ — **DOWNGRADED BY ITS OWN RECON.** `actor_id` is server-derived and cannot be faked; only the rendered label is client-supplied. **The cleanest example on record of a severity rating outliving the evidence for it.**
- ~~**"Derive `actor_label` from `auth.uid()`"**~~ — **KILLED AS A REGRESSION.** Four of six call sites deliberately pass someone other than the caller.
- ~~**"Wipe `families.timezone` and let it repopulate"**~~ — **IMPOSSIBLE.** **Client-detected values only arrive when a client shows up.**
- ~~**"Use NTP to solve the timezone problem"**~~ — **WRONG TOOL, RIGHT INSTINCT.**
- ~~**"Store a UTC offset instead of a zone name"**~~ — **KILLED.** It would look like dailies rolling at 11pm, twice a year.
- ~~**"Phaeaz's hold was mis-detected at signup"**~~ — **FALSE.** All thirteen were backfilled; detection never ran.
- ~~**"Silent auto-correct of the household timezone on any parent login"**~~ — **OVERRULED BY SCOTT, and he was right.**
- ~~**"Per-member timezones"**~~ — **REJECTED.** A chore is done at a house, not at a person.
- ~~**"Build the timezone edit path as a settings form"**~~ — **SUPERSEDED BY THE SET-ONCE HEAL.**
- ~~**"Show the timezone as a 3-letter abbreviation plus offset"**~~ — **DECLINED.**
- ~~**"Constrained plate-count search for prescription snapping"**~~ — **CUT BY SCOTT, then found ALREADY BUILT.**
- ~~**"Build Forge's equipment model around Scott's gym"**~~ — **OVERRULED BY SCOTT.** **The inventory isn't the mistake — making a user type it in is.**
- ~~**"Forge must be a full Fitbod replacement"**~~ — **SUPERSEDED.** Replaced by a user test.
- ~~**"Rebuild Forge as a standalone tool for Scott and May"**~~ — **KILLED THE SAME NIGHT IT WAS RAISED.**
- ~~**"Forge dilutes Emberhold"**~~ — **STRUCTURALLY IMPOSSIBLE.** The real risk is substitution.
- ~~**"Cardio can't be included because a PWA can't validate it"**~~ — **CONFLATED TWO THINGS.**
- ~~**"Session lane can be inferred from location"**~~ — **FALSIFIED WITHIN ONE CONVERSATION.**
- ~~**"Rest-period design leaks out of the repo"**~~ — **FALSE.**

**2026-07-26**
- ~~**"The Clean Toys rows are a fourth activeness surface"**~~ — **KILLED BY DATA.** **Both surfaces were right.**
- ~~**"The three stale dailies are a guilt pile"**~~ — **KILLED THE SAME WAY.**
- ~~**"Monthly recurrence may clamp and drift"**~~ — **STRUCTURALLY IMPOSSIBLE.**
- ~~**"The routing fork determines whether a new household sees onboarding at all"**~~ — **FALSE.** The bug was retyping. Only retyping.
- ~~**"Nothing has ever written `'training'` to `enabled_modules`"**~~ — **FALSE.** The W Drapers carried `{household,training}`.
- ~~**Screen 3's feature overview as a FOOTER under the handoff**~~ — **OVERRULED BY SCOTT, correctly.**
- ~~**"The first quest must be assigned to a named hearthmate"**~~ — **OVERRULED.**
- ~~**Scripted screenshot capture**~~ — **DECLINED.** 2–4 hours against 15–20 minutes.
- ~~**Lovable generating the screen-3 imagery**~~ — **DECLINED.** A generated approximation of your own UI reads as a mockup.
- ~~**Module intent as a first-run screen**~~ — **CUT, not killed.**
- ~~**Back navigation in the setup flow**~~ — **DECLINED for now.**

**2026-07-25 (late)**
- ~~**The four registers as four functional layers**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED, by decomposition.**
- ~~**"Sparks" as a Forge-local currency**~~ — **MOOT.**
- ~~**Four charters with three of them stubbed**~~ — **CUT.**
- ~~**The vocabulary noun "layer"**~~ — **RETIRED.**
- ~~**Renaming `enabled_modules` → `enabled_layers`**~~ — **REVERSED BEFORE IT WAS ACTED ON.**
- ~~**Porting Endure's systems into Emberhold**~~ — **CUT ON A PLATFORM WALL.**

**2026-07-25 (early)**
- ~~**Emberforge as a standalone sibling app**~~ — **KILLED.** The diagnosis was PROPORTION.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.**
- ~~**Reverting the Lovable project**~~ — **REJECTED ON EVIDENCE.** Forward migration instead.
- ~~**Loot rolls, salvage, and a Resources currency**~~ — **CUT.**
- ~~**The Hold / clans / blueprints / five construction phases**~~ — **CUT.**

**2026-07-22**
- ~~**"Match Wanmine's feature breadth"**~~ — **REJECTED ON SIGHT.** Their business gets paid at checkout and has **zero incentive to solve day 8**.
- ~~**"Capacitor / app-store path"**~~ — **DECLINED** with two named reopen triggers.
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED.**

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED.** *A scan is a sweep, never a proof.*
- ~~**"Fix the SECURITY DEFINER findings with the 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.**

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG.**
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS WRITTEN.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** ⚠️ *Correct for APPROVED quests and blind to unapproved ones. Two weeklies are stranded because of it.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED** by living-hold ambient themes.

**2026-07-18 (evening)**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

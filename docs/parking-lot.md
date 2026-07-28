# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-27.*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-27)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 THE NON-DAY-ONE FIRST RUN — the joiner flow AND "replay the intro," workshopped together.** One session, not two, because they are the same question wearing two hats — *what does someone see who is not the person who created the hold on day one?*
  - **The joiner half, and it has two flavors.** `auth.tsx` routes `create` → the five-screen flow and `join` → `/board` directly. **Every second adult and every kid in every stranger household arrives cold on a board with no guidance.** Arguably higher-value than the creator flow, because a kid's first run is where day-8 retention actually lives — and the creator flow's screens are all about *setting things up*, which a joiner does not do. **A different flow, not the same one re-routed.** A joiner needs: what a quest is, how to claim one, what embers buy, and what the PIN is for.
  - **The replay half.** `PipHelp` has a "Replay the intro" button opening a four-step sheet. An established hold can now reach `/onboarding/first-quest` by URL and be walked through cold-start copy. Options: (a) leave the sheet alone and never point it at the flow, (b) point it at a read-only version with writes disabled, (c) let it re-run the real flow and accept that it posts another quest.
  - **The two flavors are role-aware depth, not a new mechanism.** **Adult joiner** — an approver arriving into someone else's hold: approving, the Vault as an operator, what the PIN is for, and *quest authoring belongs here*. **Kid joiner** — claiming, completing, submitting, spending; **authoring is simply ABSENT, not blocked.** Absence teaches role without ever showing a kid a locked door, and it costs no migration on the busiest table in the app.
  - **Why they belong together:** a read-only, write-disabled walkthrough is plausibly the SAME artifact for both. `SetupShell` and the step registry already exist and cost nothing to reuse.
- **🔴 WHAT DOES `actor_label` MEAN? (NEW 07-27, replaces the actor-forgery fix.)** The 07-27 sweep found `actor_id` is already server-derived from `auth.uid()` and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller** — a parent approving a kid's quest correctly shows the kid's name. So the column is doing two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it, accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side from it, (c) split into two labels and let the feed compose. **(b) is jAIne's lean. This is a design call, not a bug fix.**
- **🔴 `approved_by` vs `decided_by` — which one is right? (NEW 07-27.)** Quest approval writes the PIN-verified adult from a client-side UPDATE; redemption approval writes the wall's session owner from inside the RPC. **Same tap, same wall, two different names recorded.** Quest approval's behavior is arguably better for a shared wall — it credits whoever actually authorized. Redemption's is safer — it cannot be spoofed. **Pick one, make them agree.** Bundle with the `actor_label` decision; they are the same question about identity on a shared device.
- **THE FORGE'S SHAPE — Option A vs Option B, now inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY — and the standard changed 07-27.** Set logging, automatic programming, rest timing, progressive adjustment, the reason string. **No longer "a full Fitbod replacement" as a feature bar — the bar is now a USER TEST: Scott and May stop opening Fitbod.** Achievable with one known gym and no edge cases to serve. **Cancels a $15.99/mo subscription; ~$192/yr of cost avoidance against a $636/yr target.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence of what was actually trained. No fungible currency — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.
  - **What B owes before it can be written as truth:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? — with one available modality there is nothing to type across. (3) does a collection survive the calibration check?
  - **The standing risk, named because it already happened once:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Five open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, the ignored "any member can submit a redemption attributed to another member" finding, and now the `actor_label` / `approved_by` identity question. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU, $25 on 07-22). **A one-time SKU funds a one-time year.** Hit $636 and year two needs 27 *more* new households, forever. **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Quest Log's missing `due_date` filter — by design or by omission?** **Load-bearing as a diagnostic surface** — it is how both date bugs *and* the Clean Toys question were confirmed. Arguably correct for an oversight surface, arguably a leak. **Decide deliberately.**
- **Module navigation.** Seven tabs is already one past mobile comfort. **It is not "add another tab."** **Forge's contained-route posture defers this entirely** — `/forge` gated on `enabled_modules` with no nav entry means the doorway problem waits for flip time.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it. *(Best argument: eight tables and two functions were created in production by accident. Second-best: 07-26 spent a full session with production running old frontend against a migrated database.)*
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather — set every existing household `is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`, lands in spam. **Not a Lovable job — DNS at Porkbun plus SMTP via Resend.** It is the first artifact every stranger receives and the whole signup path depends on it. ⚠️ *Inspect any NS-record request before pasting.*
- **🔴 The three signup glass checks.** Unblocked now that production is current. (1) Cold create-path signup tapping the email link on a phone. (2) Cold join-path signup — no PIN field, no error toast. (3) Original-tab path — must land on finish-setup identically to (1).
- **🔴 `master-spec.md` — FOUR folds owed, dedicated pass.** Onboarding flow, signup posture, write-once timezone, **Part II's rescoping to household-only.** **Read the current version before regenerating.**
- **The `families.timezone` update path — PROMPT WRITTEN 07-27, NOT RUN.** Bounded, finishable in twelve credits, every design question answered. Adds `timezone_confirmed_at`, a parent-gated RPC, a silent one-time heal, and a settings display + reset button. **Fixes Phaeaz on his next login without anyone interviewing him.**
- **One-line recon, free: is quest creation DISCOVERABLE from a kid's board, or merely reachable?** Four entry points are unconditioned, so a kid can get there — but nobody has checked what a kid actually sees. **Answer before anyone argues about gating it.**
- **Unapproved weekly/monthly quests never roll forward.** Two stranded on the live board right now. **A guilt pile across two of three cadences.** Likely also the STALE chip answer — verify that first, it may be one fix.
- **Recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** The anchor arithmetic *discards* the user's chosen day and the UI never says so.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone, drop three PNGs into `src/assets/`. **Test whether this is a Claude Code drop-and-point job before spending a Lovable credit on it** — the only part that genuinely needs a build lane is if layout has to change to fit real images.
- **Grant-revoke verification probe job.** Drafted 07-21, deferred twice. **Add the 07-26 table revoke to scope.** `42501` = broken grant; any app-level error = working grant.
- **The floor — avatar render fallback (🟡 glass-verify pending).** **Check the wall.**
- **Founder tier-tag verification (needs a gate-on moment).** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Measure the Smith machine offset.** Assumed 20lb bar, zero counterbalance. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.** Two minutes with a known weight.
- **Prod test-object cleanup — deserves its own session.**

---

## NEXT (soon — off the critical path)

### Onboarding, phase two

- **The non-day-one first run — joiner + replay, one session.** See OPEN DECISIONS.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week, so doing a chore early makes it reappear immediately. Wrong reward for being early.

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for V1.** The tripwire is SUBSTITUTION — a Forge session happening instead of a Gate B item rather than alongside one.*

- **Build from scratch against `families` / `profiles` — not by resurrection.** The 07-25 prototype was dropped correctly; it was built on an invented parallel tenancy. The rebuild reuses **no schema** and **one artifact**: `src/lib/progression.ts` + its ten tests. **Household-scoped via `current_family_id()`, members from the existing hearthmate table, avatars via the existing portrait component. A module never mints a person.**
- **Containment posture (Scott's call, 07-27):** route at `/forge`, gated on `families.enabled_modules` containing `'fitness'`, **no nav entry.** The switch already exists and needs no build. A stranger household never renders it. **The flip is two things, not one** — the module gate (built) and a doorway in navigation (unsolved, deferred).
- **🔴 THE ENGINE GAP, found 07-27:** `progression.ts` has **no progression axis except load.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo, not stubbed. **It cannot express "175 instead of 185, so give me 8 instead of 5"** — the named differentiator, and also the only fix for the dumbbell rack's +50% bottom-end jumps. **Extending it is a Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits, desktop-bound.** What the rep axis should actually *do* is a rest-period design conversation.
- **What the engine already does, verified 07-27:** takes inventory as a parameter (so presets are pure data) · bounded subset-sum over finite plate counts · `"timed"` equipment type returning `null` (cardio hook) · pure stateless e1RM that cannot contaminate across equipment types. **Better shape than assumed.**
- **Slice one, when it happens: log a dumbbell set and get the reason string.** Two builds, frame then content. Schema (~five tables: exercises, equipment profiles, sessions, entries, sets) + gated route + themed empty state, then the loop. **Out of slice one:** program generation, cardio UI, bands, plate-loaded snapping, LLM anything, activity-feed writes.
- **Offline is slice 1.5, not slice one.** A local write buffer plus reconciliation is a distributed-systems problem, not a screen. Schema must not preclude it; the build ships online-first and the buffer lands before any garage test.
- **Cardio is IN and it is prescribed, not autoregulated.** Time and optional resistance, self-reported like everything else. No HR, no validation, no reason string — **nothing computes it, so nothing explains it.** A session must hold **entries of two shapes** (strength-with-sets, cardio-with-time) and must allow **mixed sessions** from day one, because "10 minute bike warmup" belongs at the front of a lift day.
- **Bands are out of v1.** Ordinal resistance (light → ultra heavy) is a rank, not a load, and produces no load set. **But once a non-load entry shape exists for cardio, bands stop being a second engine and become a rank plus reps.** Door unwelded, not opened.
- **The differentiator is explainability, and its unbuilt half is rep compensation.** *"All sets clean under target — adding 10 lb"* is the reason string. **"You only have 175? Then give me 8 instead of 5" is the reason string running in reverse** — the user tells the app something and the app tells them what to do about it. **That is where Fitbod goes silent.** Needs a validity floor: below some deviation the app must say *"that's a different exercise now"* rather than compute an equivalence it doesn't have.
- **`prescribed` and `actual` are two fields, never one.** If the app logs what it told you to lift rather than what you lifted, every downstream e1RM is silently wrong and unreconstructable. **Autoregulation reads actual, always.**
- **Progression logic never lives in Postgres.** A garage has unreliable connectivity. Supabase is the sync target, never the compute.
- **The garage wall is an *input* device, not a display.** Big rest timer, plate math readable from the rack, big set-entry tiles. **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Multi-member concurrent sessions are a design requirement.** **Rest timer is derived (`now − last set.logged_at`), never stored.** **Attribution, not concurrency, is the real risk:** make the tap the selection rather than a persistent "current user" mode that goes stale mid-session.
- **No PIN on set logging.** Friction mid-workout is the enemy and the stakes are nil.
- **Participation is a per-member flag, not a role check** — when it's needed. **Do not build it now.**
- **Free/paid no longer applies.** Nobody buys an LLM tier in a two-person app.

### Option B ideas — parked until A has been used for a month

- **The tool wall** — a collection of forged implements whose material composition is a readout of which modalities you actually trained.
- **Component-level chronology** — an item built piece by piece, each taking the material of what you trained while it was forged. **Un-gameable because the material is earned before you see it.**
- **Item aging** — a hammer used 200 times shouldn't look new. Patina as a record of work.
- **The shared-barbell plate delta** — *"strip 2×25, add 2×45"* when two people alternate at different loads. **Arguably belongs in Option A** — a tool feature wearing a game's clothes.
- **The pure/mixed trap** — if a single-material item reads as cleaner than a mixed one, the app punishes conditioning. **Mixed and pure must be different-beautiful, never ranked.**

### Endure — the separate product

- **🅿️ Parked, not killed, and the block is platform.** Its fitness bridge requires Apple Health / Google Fit with heart rate as gatekeeper. **There is no HealthKit web API**, and the PWA posture is LOCKED. **Principles ported; systems did not.**
- **Endure is the app that does it DIFFERENT. Forge is the app that does it the SAME, for less money.** Keeping them separate is the discipline working — confusing them is what produced the tool wall.
- **Reopen trigger: the same two that reopen Capacitor.** Endure does not create a third.

### Everything else

- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is *built*, not before. **The trigger has not fired — Forge is designed, not built.** Do not re-litigate, do not delete.
- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** Wanmine ships two-way Google/iCloud/Outlook/Yahoo sync plus TeamSnap and GameChanger as table stakes. Emberhold's calendar is a closed loop, and the calendar is one of the two backbones. **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide before Gate D**, because the landing page makes a promise either way.
- **The service worker's priority may rise with Forge.** A garage has unreliable connectivity, so an offline shell is *function* for Forge and *polish* for Emberhold. **Does not change its Gate B priority today**, because Forge is not built — but if Forge ever ships, this stops being polish.
- **Ask Lovable what `sandbox_exec` is.** Pre-existing platform role holding EXECUTE on every function in `public`. **One question, not a project.**
- **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`.
- **"Forgot PIN" confirm() copy.** Claims "only the account owner" — inaccurate.
- **`decisions.md` header nit.** The prose "Status tiers" line omits SUPERSEDED. One-line fix on next touch.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall`; grep every LOCKED decision against the codebase; retire/rebuild `avatar-review.tsx`.
- **Re-forge reach across the 13.** Only the logging-in member is prompted.
- **Vault favorites → real per-profile persistence.** Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward — DE-PRIORITIZED.** Starter quests run 5–30 embers, so the cheapest reward is two to five chores away. **That is the habit, not a wall.**
- **Wall event-pill member color — dot-vs-full-tint.** **Wall ticker speed.**
- **Multi-day calendar events (QA #7).** **Calendar alerts (QA #6)** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream, opened 2026-07-22*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Cosmetics only aid *acquisition* where a non-customer can see them. Emberhold cosmetics live on private phones. **The wall is the sole exception.**

**A sharper test than "delight vs. function": is this thing a CHOICE or a MEMORY?** Expression is choice — always free, always reversible, outcome-neutral. A **mark** is memory — earned through something that actually happened, and what is paid for is the *ritual of permanence*.

**Hard constraint:** all of it is downstream of day-8. A catalog amplifies retention that exists; it cannot manufacture retention that doesn't.

**#1 — Living-hold ambient themes (SKU #2).** Each register as a *living, moving hold* rather than a recolor. **Free = the hearth** (steady even glow, stays a full tool). **Forge = heat/drive.** **Hall = revelry.** **Garden = growth.** **Keep = restraint** (alive by being the calmest — deliberate motion, NOT static).
  - **Register is the aesthetic *source*, never the entitlement.** A hold runs mixed registers.
  - **All themes modulate the ember-lit floor, never replace it.**
  - **Canvas particles YES, bespoke video NO.**
  - **Kiosk-hardening is a hard requirement, not polish** — `prefers-reduced-motion`, visibility-pause, frame throttling, static fallback.
  - **Build order de-risks itself: Keep first.** Lowest particle count, doubles as the performance proving-ground.

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow already exists on disk with zero marginal authoring labor.

**#3 — Borders, frames, phone-only flourishes.** Sell only to people already inside. Build last or never.

---

- **The timezone nudge — a signal, never an inference (NEW 07-27).** If the household's stored zone has disagreed with every adult's device for ~30 consecutive days, Pip mentions it once. **A human confirms; the system never acts.** Correctly ignores a business trip because you came home. **Only if the set-once heal plus the reset button prove insufficient — which they probably won't.**
- **The "how Scott & jAIne work" collaboration profile.** **07-27 supplied two more entries and they share the shape of all the others: jAIne stated inference as fact.** Invented a wife's name ("Erin") out of nothing and asserted it. Asserted that rest-period design was leaking out of the repo, in a workflow Scott has been reliably running for weeks. Prior entries: claimed nothing had ever written `'training'`; called four Clean Toys rows a guilt pile without reading `archived`; claimed the routing fork determined whether a household saw onboarding; got the day of the week wrong. **Also: prescribed a "30-second glass check" on a field the frontend cannot read, and left it on the board for three sessions.** The profile should encode: **fetch before producing · decompose before you promote · read what landed against what was briefed · state a hypothesis as a hypothesis · never prescribe a check the surface cannot perform · and never invent a fact about Scott's life to fill a gap.** *Scott has now overruled jAIne on the naming call, the module-merge call, the currency call, the layer-collapse call, the screen-3 call, the assign-only call, the plate-inventory call, and the silent-timezone call.*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration.
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version: wall redemption/spend commit, deployment gremlins (Safari, PWA display-mode, screen-sleep), P4×L8 on the wall's write surface, favorites on the wall.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is not a channel — it's the invite/deep-link loop.** Then: one earnest post in r/daddit / r/Mommit / r/homeschool; Product Hunt + PWA directories; Habitica's existing audience; **the homeschool angle (🔵 VALIDATED 07-10).** **Don't become a content marketer.**
- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-27**
- ~~**"Activity-log actor forgery is the top open security item"**~~ — **DOWNGRADED BY ITS OWN RECON.** `actor_id` is server-derived from `auth.uid()` and cannot be faked; only the rendered label is client-supplied. **A feed that can display a wrong name over a truthful row is not a corrupted audit trail.** Kept because the *shape* of the concern was right — a receipt surface must be trustworthy — and because it is the cleanest example on record of a severity rating outliving the evidence for it across three sessions.
- ~~**"Derive `actor_label` from `auth.uid()`"**~~ — **KILLED AS A REGRESSION.** Four of six call sites deliberately pass someone other than the caller. The fix would rewrite every completed-quest feed entry to the approving parent's name instead of the kid who did the chore. **jAIne had described this fix twice before reading the call sites.**
- ~~**"Wipe `families.timezone` and let it repopulate"**~~ — **IMPOSSIBLE, AND THE REASON IS GENERAL.** Timezone detection is a browser API; it runs on the user's device when they are present. **Postgres cannot go get it.** Wiping produces thirteen NULLs and breaks rollover for holds that are currently correct. **The rule: client-detected values only arrive when a client shows up.**
- ~~**"Use NTP (`pool.ntp.org` / `time.nist.gov`) to solve the timezone problem"**~~ — **WRONG TOOL, RIGHT INSTINCT.** NTP answers *what instant is it*, which nothing in Emberhold is confused about. The question is *which calendar day is it for this household*, and the billion-times solution is the IANA tz database, which Postgres ships and `household_today()` already uses. **The time part has no engineering left in it.**
- ~~**"Store a UTC offset (`-8`) instead of a zone name"**~~ — **KILLED.** Offsets aren't stable: `America/Los_Angeles` is UTC−8 in winter and UTC−7 in summer. **In Emberhold that wouldn't look like a timezone bug — it would look like dailies rolling over at 11pm, twice a year.**
- ~~**"Phaeaz's hold was mis-detected at signup"**~~ — **FALSE.** Timezone capture shipped 07-26; all thirteen holds predate it. They were **backfilled** with the default when the NOT NULL column was added 07-23. Detection never ran for any of them. **The system is correct going forward and carries a one-time debt backward — two different problems.**
- ~~**"Silent auto-correct of the household timezone on any parent login"**~~ — **OVERRULED BY SCOTT, and he was right.** A parent travelling for work would move the whole household's clock for a week; May's dailies would expire mid-evening and nobody at home would connect the two. **A household's timezone is where the household lives, and the traveller's device is exactly the wrong source of truth.**
- ~~**"Per-member timezones"**~~ — **REJECTED.** Contradicts the 07-23 decision that the *household's* day is what matters, and it should — a chore is done at a house, not at a person.
- ~~**"Build the timezone edit path as a settings form"**~~ — **SUPERSEDED BY THE SET-ONCE HEAL.** A form builds a lever with nobody's hand on it: someone still has to know Phaeaz's real zone and go set it. **The heal fixes the thirteen with no interviews; the button is for the once-a-decade relocation.** Same RPC, two triggers.
- ~~**"Show the timezone as a 3-letter abbreviation plus offset (`PST (−8)`)"**~~ — **DECLINED.** Ambiguous (CST is US Central, China Standard *and* Cuba Standard), flips label twice a year on a setting nobody touched, and **doesn't exist for every zone** — `America/Sao_Paulo` renders as `-03`. **Store IANA, render `Intl.DateTimeFormat` with `timeZoneName: 'longGeneric'` → "Pacific Time."** Season-neutral, localized, one call, nothing to maintain.
- ~~**"Constrained plate-count search for prescription snapping"**~~ — **CUT BY SCOTT, then found to be ALREADY BUILT.** Scott's call was standards-plus-manual-override rather than modelling finite plate counts. The 07-27 recon then found `progression.ts` already does bounded subset-sum over `plate_pair` rows with `quantity`. **Kept because it cost three messages of jAIne chasing an inventory the design didn't need.**
- ~~**"Build Forge's equipment model around Scott's gym"**~~ — **OVERRULED BY SCOTT.** *"Less transferable and approachable if this goes beyond my gym. We need to think in terms of standards."* **The inventory isn't the mistake — making a user type it in is.** Presets write standard inventories; a specific gym becomes preset + overrides. **The test: if Scott's gym can't be expressed that way, the preset model is wrong.**
- ~~**"Forge must be a full Fitbod replacement"**~~ — **SUPERSEDED 07-27.** Read literally the standard has no exit; Fitbod has a team. **Replaced by a user test: Scott and May stop opening Fitbod.**
- ~~**"Rebuild Forge as a standalone tool for Scott and May"**~~ — **KILLED THE SAME NIGHT IT WAS RAISED.** Scoping to two users makes standalone *worse*: the infrastructure cost (auth, tenancy, member model) stays fixed while the audience shrinks to two. **That is the exact proportion failure the 07-25 teardown deleted.** The merge decision was made on infrastructure grounds and audience does not touch any of them.
- ~~**"Forge dilutes Emberhold"**~~ — **STRUCTURALLY IMPOSSIBLE.** `enabled_modules` plus a gated route with no nav entry means a stranger household never renders it. **You cannot dilute a product with something it never shows anyone.** The real risk is substitution — a Forge session instead of a Gate B item.
- ~~**"Cardio can't be included because a PWA can't validate it"**~~ — **CONFLATED TWO THINGS.** Can't be *validated* ≠ can't be *included*. Time and resistance are self-reported like every other number in the app. **Cardio is in, prescribed rather than autoregulated.**
- ~~**"Session lane can be inferred from location (gym vs desk)"**~~ — **FALSIFIED WITHIN ONE CONVERSATION.** Scott was at a keyboard with no Lovable credits, and Lovable runs fine from a phone. **Credits and hands are independent switches; only Claude Code is genuinely desktop-bound.**
- ~~**"Rest-period design leaks out of the repo"**~~ — **FALSE, and jAIne asserted it about a habit Scott has been reliably running.** Conversations carry across devices and Scott commits the outputs. **The capture muscle is working; jAIne pattern-matched to a known project failure instead of reading the evidence.**

**2026-07-26**
- ~~**"The Clean Toys rows are a fourth activeness surface"**~~ — **KILLED BY DATA.** The first three carry `archived = true`. **Both surfaces were right.** Carried since 07-23 as a check that could never have been performed — `due_date` is backend-only.
- ~~**"The three stale dailies are a guilt pile"**~~ — **KILLED THE SAME WAY.**
- ~~**"Monthly recurrence may clamp and drift"**~~ — **STRUCTURALLY IMPOSSIBLE.** The arithmetic is `date_trunc('month', today) + 1 month`; the day-of-month never enters it.
- ~~**"The routing fork determines whether a new household sees onboarding at all"**~~ — **FALSE.** `runSetup()` always routes `create` → the flow. **The bug was retyping. Only retyping.**
- ~~**"Nothing has ever written `'training'` to `enabled_modules`"**~~ — **FALSE.** The W Drapers carried `{household,training}`.
- ~~**Screen 3's feature overview as a FOOTER under the handoff**~~ — **OVERRULED BY SCOTT, correctly.**
- ~~**"The first quest must be assigned to a named hearthmate"**~~ — **OVERRULED.** The board has an Open Bounties strip, and `add-family` is skippable so a hold can reach screen 1 with nobody to assign to.
- ~~**Scripted screenshot capture (Puppeteer/Playwright)**~~ — **DECLINED.** 2–4 hours against 15–20 minutes of manual setup.
- ~~**Lovable generating the screen-3 imagery**~~ — **DECLINED.** A generated approximation of your own UI reads as a mockup.
- ~~**Module intent as a first-run screen**~~ — **CUT, not killed.** Revisit when Forge ships.
- ~~**Back navigation in the setup flow**~~ — **DECLINED for now.**

**2026-07-25 (late)**
- ~~**The four registers as four functional layers**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED, by decomposition.** Only fitness failed to decompose, and structurally.
- ~~**"Sparks" as a Forge-local currency**~~ — **MOOT.** Option A has no currency at all.
- ~~**Four charters with three of them stubbed**~~ — **CUT.**
- ~~**The vocabulary noun "layer"**~~ — **RETIRED.**
- ~~**Renaming `enabled_modules` → `enabled_layers`**~~ — **REVERSED BEFORE IT WAS ACTED ON.**
- ~~**Porting Endure's systems into Emberhold**~~ — **CUT ON A PLATFORM WALL, NOT A PREFERENCE.**

**2026-07-25 (early)**
- ~~**Emberforge as a standalone sibling app**~~ — **KILLED.** The diagnosis was PROPORTION, not family resemblance.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.**
- ~~**"Emberfit" / "Emberforge" / "Embermill" / "Emberworks" / "Ironhold"**~~ — **MOOT.**
- ~~**Reverting the Lovable project**~~ — **REJECTED ON EVIDENCE.** Version history restores code only. **Forward migration instead. A standing rule.**
- ~~**Loot rolls, salvage, and a Resources currency (from Endure)**~~ — **CUT.**
- ~~**The Hold / clans / blueprints / five construction phases (from Endure)**~~ — **CUT.**

**2026-07-22**
- ~~**"Match Wanmine's feature breadth"**~~ — **REJECTED ON SIGHT.** Their business gets paid at checkout and has **zero incentive to solve day 8**.
- ~~**"Capacitor / app-store path"**~~ — **DECLINED** with two named reopen triggers.
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED.** **Nobody discovers a free lifestyle app by browsing.**

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED AS A VERIFICATION METHOD.** *A scan is a sweep, never a proof.*
- ~~**"Fix the SECURITY DEFINER findings with the 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.**

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG.**
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS EVER WRITTEN.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** ⚠️ *That call was correct for APPROVED quests and blind to unapproved ones. Two weeklies are stranded because of it.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED** by *living-hold ambient themes*.

**2026-07-18 (evening)**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-26.*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-26)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 THE NON-DAY-ONE FIRST RUN — the joiner flow AND "replay the intro," workshopped together (NEW 07-26).** Scott's call: these are one session, not two, because they are the same question wearing two hats — *what does someone see who is not the person who created the hold on day one?*
  - **The joiner half.** `auth.tsx` routes `create` → the five-screen flow and `join` → `/board` directly. **Every second adult and every kid in every stranger household arrives cold on a board with no guidance.** Arguably higher-value than the creator flow, because a kid's first run is where day-8 retention actually lives — and the creator flow's screens are all about *setting things up*, which a joiner does not do. **A different flow, not the same one re-routed.** A joiner needs: what a quest is, how to claim one, what embers buy, and what the PIN is for.
  - **The replay half.** `PipHelp` has a "Replay the intro" button opening a four-step sheet. The five-screen flow now exists, and an established hold can reach `/onboarding/first-quest` by URL and be walked through cold-start copy. Options: (a) leave the sheet alone and never point it at the flow, (b) point it at a read-only version with writes disabled, (c) let it re-run the real flow and accept that it posts another quest.
  - **Why they belong together:** a read-only, write-disabled walkthrough is plausibly the SAME artifact for both — someone who did not build the hold does not need to build one, and neither does someone replaying. **Scope them as one thing and find out.** `SetupShell` and the step registry already exist and cost nothing to reuse.
- **🔴 THE FITNESS MODULE'S SHAPE — Option A vs Option B.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY — AND FULL-FEATURED. LOCKED 07-26.** Set logging, automatic programming, rest timing, progressive adjustment, the reason string. No game, no currency, no collection. **A is not a thin MVP with the fun bolted on later — it is a complete Fitbod replacement.** If a lifter still opens Fitbod for anything, A has not shipped. **Buildable now. Cancels a $15.99/mo subscription this month.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence of what was actually trained. No fungible currency — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.
  - **What B owes before it can be written as truth:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? — typing by modality contradicts a LOCKED call in `endure-canonical` that all modalities must produce equal value, and with one available modality there is nothing to type across anyway. (3) does a collection survive the calibration check — *this is a family chore game with your kids' faces in it*?
  - **The standing risk, named because it already happened once:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** (1) the `adults_only` rewards audience read, (2) the `parents_only` quest details read, (3) the ignored scanner finding "adult PIN lock isn't tied to real permission checks," and (4) the ignored finding "any member can submit a redemption attributed to another member." Items 3 and 4 are marked **Ignored** in the Lovable dashboard — **and that panel will never tell you they're conditional. If this fork is ever decided toward per-member auth, two dismissed findings come back to life as real work.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU, priced $25 on 07-22). **A one-time SKU funds a one-time year.** Hit the $636 target and year two needs 27 *more* new households, forever. The catalog reframe is the answer — a retained household becomes worth $25 + $10 + $5. **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Quest Log's missing `due_date` filter — by design or by omission?** It shows every quest with no gate, so it displays future-dated successors the board correctly hides. **It is load-bearing as a diagnostic surface** — it is how both date bugs *and* the Clean Toys question were confirmed. Arguably correct for an oversight surface, arguably a leak. **Decide deliberately.** If it stays unfiltered, consider a visual marker on not-yet-due instances rather than silence.
- **Module navigation.** Seven tabs is already one past mobile comfort, and an enabled module needs somewhere to live. **It is not "add another tab."** Blocks nothing until a module actually ships.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it. *(Best argument yet: eight tables and two functions were created in production by accident and had to be dropped in production. Second-best: 07-26 spent a full session with production running old frontend against a migrated database.)*
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** (a) a permission tier *inside* a hold, above parent — or (b) a cross-hold super-admin for tier-2 support? Different builds. **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both one-line data changes, no build. ⚠️ **`authenticated` and `anon` are both correctly denied UPDATE on `is_founder`; the grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 PUBLISH.** Two days of frontend sit in dev against a live-migrated database. It has held only because `_timezone` was added with `DEFAULT NULL`. **Nothing else on this list matters until this is done.**
- **🔴 The three signup glass checks.** (1) Cold create-path signup tapping the email link on a phone — the case that has always been broken. (2) Cold join-path signup — no PIN field, no error toast. (3) Original-tab path — must now land on finish-setup identically to (1), which is the proof the fork is closed rather than moved.
- **🔴 `master-spec.md` — three folds owed, dedicated pass.** Onboarding flow, signup posture, write-once timezone. **Read the current version before regenerating.** First work after publishing.
- **🔴 Adult-gate quest creation — decided 07-26, not built.** An RLS INSERT-policy clause **plus** the client gate, together; a client gate alone is theatre. ⚠️ **Ships with the `FirstQuestDoorway` and onboarding first-quest role conditions, or it walks a kid into a wall behind the mascot.** Also unconditioned today: the FAB in `BottomBar.tsx`, `board.tsx`, `campaign.$id.tsx`, `QuickAddTray.tsx`.
- **Monday 2026-07-27 — the recurring reappearance check.** Five weeklies staged at `due_date = 2026-07-27`. **The trigger's half is already proven by data**, so if nothing appears there is exactly one candidate: `isActiveQuest`. Not a build; open the board and look.
- **🔴 `families.timezone` has no update path.** Write-once at family INSERT. Thirteen holds pinned to the default forever; any hold created while travelling is permanently wrong. **Gate B item.** Needs an RPC and somewhere to call it from — hold settings is the obvious home.
- **Unapproved weekly/monthly quests never roll forward.** Nothing rolls them; successors are minted only by approval. Two stranded on the live board right now. **A guilt pile across two of the three cadences.** Likely also the STALE chip answer — verify that first, it may be one fix.
- **Recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** Not cosmetic any more: the anchor arithmetic *discards* the user's chosen day, so a monthly created on the 15th recurs on the 1st and the UI currently never says so.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone, drop three PNGs into `src/assets/`. **Not scripted — DECLINED 07-26.** The demo hold pays for itself again at Gate D for landing-page assets.
- **Grant-revoke verification probe job.** Brief drafted 07-21, deferred twice. **Add the 07-26 table revoke to scope.** The whole job hinges on one distinction: `42501` = broken grant; any app-level error = working grant.
- **Eyeball `/setup/intent`.** Never loaded by a human. Decide its fate in the same pass.
- **The floor — avatar render fallback (🟡 glass-verify pending).** Verify on the glass; **check the wall.**
- **Founder tier-tag verification (needs a gate-on moment).** Flip gate ON → confirm 32 lock / 16 open → flip back OFF.
- **Prod test-object cleanup — now deserves its own session.** Including the several throwaway holds created 07-26 while testing onboarding.

---

## NEXT (soon — off the critical path)

### Onboarding, phase two

- **The non-day-one first run — joiner + replay, one session.** See OPEN DECISIONS.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week, so doing a chore early makes it reappear immediately. Wrong reward for being early.
- **Activity-log actor forgery.** `append_activity` takes `_actor_label text` from the client. Derive server-side from `auth.uid()`. **The activity log is the receipt surface**, and a forged actor corrupts the record an adult would use to catch anything else. **Now the top open security item.**

### The fitness module — Option A

*The best ROI item on the board and it blocks nothing. Fitbod at $15.99/mo is ~$192/yr against a $636/yr break-even target — roughly a third of break-even recovered with no paywall, no funnel, and no Gate E dependency.*

- **Build from scratch against `families` / `profiles` — not by resurrection.** The 07-25 prototype was dropped and that was correct; it was built on an invented parallel tenancy. The rebuild reuses **no schema** and **one artifact**: `src/lib/progression.ts` + its ten tests, which survived because they are pure, table-agnostic, and have no Supabase imports. **Household-scoped via `current_family_id()`, members read from the existing hearthmate table, avatars via the existing portrait component. A module never mints a person.**
- **The functional core, in build order:** (1) offline-first logging, (2) set entry that survives a sweaty one-handed rest period, (3) RPE/RIR on every set, (4) e1RM trend + weekly volume, (5) rest timing, (6) template-driven program/block authoring.
- **The differentiator is explainability, not AI.** Deterministic autoregulation off RPE, surfacing a one-sentence reason (*"all sets clean under target — adding 10 lb"*). **The reason string is the product.**
- **Free/paid is LOCKED and narrower than it first looked.** Deterministic autoregulation, template-driven auto-programming, rest timing and progressive adjustment are **FREE**. **Only LLM-generated weekly programming is PAID** — the sole feature with a real per-user marginal cost.
- **Progression logic never lives in Postgres.** A garage has unreliable connectivity. Supabase is the sync target, never the compute.
- **The garage wall is an *input* device, not a display.** Big rest timer, plate math readable from the rack, big set-entry tiles. **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Multi-member concurrent sessions are a design requirement.** **Rest timer is derived (`now − last set.logged_at`), never stored.** **Attribution, not concurrency, is the real risk:** make the tap the selection rather than a persistent "current user" mode that goes stale mid-session.
- **No PIN on set logging.** Friction mid-workout is the enemy and the stakes are nil.
- **Participation is a per-member flag, not a role check** — when it's needed. **Do not build it now.**

### Option B ideas — parked until A has been used for a month

- **The tool wall** — a collection of forged implements whose material composition is a readout of which modalities you actually trained. **Hybrid recognition for free, with no classification logic.**
- **Component-level chronology** — an item built piece by piece, each taking the material of what you trained while it was forged. **Un-gameable because the material is earned before you see it.**
- **Item aging** — a hammer used 200 times shouldn't look new. Patina as a record of work; kills completionism and ports the heat ramp down to the item.
- **The shared-barbell plate delta** — *"strip 2×25, add 2×45"* when two people alternate at different loads. **Arguably belongs in Option A** — a tool feature wearing a game's clothes.
- **The pure/mixed trap** — if a single-material item reads as cleaner than a mixed one, the app punishes conditioning. **Mixed and pure must be different-beautiful, never ranked.**

### Endure — the separate product

- **🅿️ Parked, not killed, and the block is platform.** Its fitness bridge requires Apple Health / Google Fit with heart rate as gatekeeper. **There is no HealthKit web API**, and the PWA posture is LOCKED (Capacitor DECLINED 07-22). **Principles ported; systems did not.**
- **Reopen trigger: the same two that reopen Capacitor** — PWA push proving load-bearing at Gate E, or an appliance play after Gate E returns retention driven by the wall. **Endure does not create a third trigger.**

### Everything else

- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER (07-26).** Scott's call: finalized when fitness/Forge is built, not before. Module intent has nothing to point at until the module exists, so it stops being an open question and becomes a dependency of the fitness build. **Do not re-litigate it in the meantime, and do not delete it either.**
- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** Competitive scan of **Wanmine** shows two-way Google/iCloud/Outlook/Yahoo sync **plus TeamSnap and GameChanger** as table stakes in the ambient-family-display category. Emberhold's calendar is a closed loop, and the calendar is one of the two backbones. **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide which before Gate D**, because the landing page makes a promise either way.
- **Ask Lovable what `sandbox_exec` is.** A role holding EXECUTE on every function in `public`. Pre-existing, so not drift — almost certainly platform infrastructure. Surfaced again on 07-26. **One question, not a project.**
- **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile.
- **Test-quest cleanup.** Fold into the broader prod test-object cleanup in `status.md`.
- **"Forgot PIN" confirm() copy.** Claims "only the account owner" — inaccurate.
- **`decisions.md` header nit.** The prose "Status tiers" line omits SUPERSEDED, which *is* present in the format block below it. One-line fix on next touch.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall`; grep every LOCKED decision against the codebase (the "did it land" cross-check); retire/rebuild `avatar-review.tsx`.
- **Re-forge reach across the 13.** Only the logging-in member is prompted. Options: prompt each member on next active session, or a hold-owner "N hearthmates need re-forging" nudge.
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model. Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward — DE-PRIORITIZED 07-26.** The concern was that a 25-ember floor put the first reward out of reach. Starter quests run 5–30 embers, so the cheapest reward is two to five chores away. **That is the habit, not a wall.** Still fine to add; no longer urgent.
- **Wall event-pill member color — dot-vs-full-tint.** **Wall ticker speed.**
- **Multi-day calendar events (QA #7).** **Calendar alerts (QA #6)** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift (confirmed live 07-26).

---

## LATER (backlog)

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream, opened 2026-07-22*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Cosmetics only aid *acquisition* where a non-customer can see them. Emberhold cosmetics live on private phones. **The wall is the sole exception** — the only semi-public Emberhold artifact.

**A sharper test than "delight vs. function": is this thing a CHOICE or a MEMORY?** Expression is choice — always free, always reversible, outcome-neutral. A **mark** is memory — earned through something that actually happened, and what is paid for is the *ritual of permanence*, not the thing itself. Themes are choice. An artifact produced by six months of real effort is a memory.

**Hard constraint:** all of it is downstream of day-8. A catalog amplifies retention that exists; it cannot manufacture retention that doesn't.

**#1 — Living-hold ambient themes (SKU #2; household-level, cosmetic, delight-not-access).** Each register as a *living, moving hold* rather than a recolor. **Free = the hearth** (belonging; steady even glow, stays a full tool). **Forge = heat/drive** (bellows, rising embers, hotter pulse). **Hall = revelry** (lantern flicker, drifting warm light). **Garden = growth** (slow sway, pollen, fireflies). **Keep = restraint** (alive by being the calmest — deliberate motion, NOT static).
  - **Register is the aesthetic *source*, never the entitlement.** A hold runs mixed registers.
  - **All themes modulate the ember-lit floor, never replace it.**
  - **Canvas particles YES, bespoke video NO.**
  - **Kiosk-hardening is a hard requirement, not polish** — `prefers-reduced-motion`, visibility-pause, frame throttling, static fallback. A *scope-shaper*.
  - **Build order de-risks itself: Keep first.** Lowest particle count, doubles as the performance proving-ground.

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow already exists on disk with zero marginal authoring labor. Related: the seasonal frost set.

**#3 — Borders, frames, phone-only flourishes.** Sell only to people already inside. Build last or never.

---

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions, so every cold session starts from the best footing. **07-26 supplied four more entries and they share one shape:** jAIne stated inference as finding. Claimed nothing had ever written `'training'` (one row had); called four Clean Toys rows a guilt pile without reading the `archived` column (three were archived); claimed the routing fork determined whether a household saw onboarding at all (it never did); and got the day of the week wrong while telling Scott to stop carrying a test. **Also: jAIne prescribed a "30-second glass check" on a field the frontend cannot read, and left it on the board for three sessions.** The profile should encode: **fetch before producing · decompose before you promote · read what landed against what was briefed · state a hypothesis as a hypothesis and read before asserting · and never prescribe a check the surface cannot perform.** *Scott has now overruled jAIne on the naming call, the module-merge call, the currency call, the layer-collapse call, the screen-3 call, and the assign-only call.*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration.
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version: wall redemption/spend commit, deployment gremlins (Safari, PWA display-mode, screen-sleep), P4×L8 on the wall's write surface, favorites on the wall.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is not a channel — it's the invite/deep-link loop.** Then: one earnest post in r/daddit / r/Mommit / r/homeschool; Product Hunt + PWA directories; Habitica's existing audience; **the homeschool angle (🔵 VALIDATED 07-10).** **Don't become a content marketer.** *(27 strangers over twelve months is roughly one every other week.)*
- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-26**
- ~~**"The Clean Toys rows are a fourth activeness surface"**~~ — **KILLED BY DATA.** Four rows; the first three carry `archived = true`. Quest Log applies no `due_date` filter and correctly showed them; the member profile applies `isActiveQuest` and correctly hid them. **Both surfaces were right. The LOCKED activeness decision stands as written.** Carried since 07-23 as a 30-second check that could never have been performed — `due_date` is backend-only, and jAIne kept prescribing a frontend read of it.
- ~~**"The three stale dailies are a guilt pile"**~~ — **KILLED THE SAME WAY.** jAIne built a four-row pattern out of a column it had not read. `roll_missed_dailies()` archives the predecessor and carries `claimed` into the successor exactly as designed.
- ~~**"Monthly recurrence may clamp and drift (Jan 31 → Feb 28 → forever)"**~~ — **KILLED, STRUCTURALLY IMPOSSIBLE.** The arithmetic is `date_trunc('month', today) + 1 month`, not `due_date + 1 month`. The day-of-month never enters it. Worth keeping because the concern was *correct in shape* — a re-derived anchor would drift — and the reason it doesn't is a design property worth knowing.
- ~~**"The routing fork determines whether a new household sees onboarding at all"**~~ — **FALSE, KILLED.** `runSetup()` always routes a `create` signup to `/onboarding/add-family` regardless of whether the data came from the stash or a re-typed form. **The bug was retyping. Only retyping.** Recorded because jAIne asserted the bigger failure and then built a recommendation on top of it.
- ~~**"Nothing has ever written `'training'` to `enabled_modules`"**~~ — **FALSE.** The W Drapers carried `{household,training}` from the 07-25 prototype night. It was in `status.md` and `decisions.md` as a stated fact, sourced from jAIne, and corrected on 07-26.
- ~~**Screen 3's feature overview as a FOOTER under the handoff**~~ — **OVERRULED BY SCOTT, correctly.** A footer under a handoff is where things go to be ignored. If Lists/Calendar/Campaigns are worth showing they are worth a beat; if they are not worth a beat they should not be in the flow at all. jAIne's amendment was trying to have it both ways, and it also quietly reversed what Scott had specified on 07-12.
- ~~**"The first quest must be assigned to a named hearthmate"**~~ — **OVERRULED, and wrong for two reasons.** The board has an Open Bounties strip, so forcing assignment teaches that quests belong to people rather than that work can be claimed. And `add-family` is skippable, so a hold can reach screen 1 with nobody to assign to — the instruction had no answer for its own edge case.
- ~~**Scripted screenshot capture (Puppeteer/Playwright)**~~ — **DECLINED.** Code priced it at 2–4 hours against 15–20 minutes of manual setup, for three PNGs that get re-taken maybe twice a year. Scripting only pays off if the UI churns constantly. **Seed a demo hold by hand and screenshot from the phone.**
- ~~**Lovable generating the screen-3 imagery**~~ — **DECLINED.** Lovable can generate imagery but what the screen needs is a screenshot of the *actual* surface. A generated approximation of your own UI reads as a mockup and misleads the first-time user about what they are about to open.
- ~~**Module intent as a first-run screen**~~ — **CUT FROM THE 07-26 BUILD, not killed.** The 07-25 decision has intent launching a *second setup flow*, and there is no flow to launch because fitness does not exist. The parking-lot line saying "ship two options (Household, Training)" contradicted the line saying "do not offer modules that don't exist"; **the second one wins.** Revisit when fitness ships.
- ~~**Back navigation in the setup flow**~~ — **DECLINED for now.** One-directional is simpler, nothing in the flow is destructive, and skip already provides an exit. Cost: a typo'd quest cannot be fixed until the board.

**2026-07-25 (late)**
- ~~**The four registers as four functional layers (Forge / Keep / Garden / Hall)**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED, by decomposition.** Keep is quests. Garden is quests. Hall is lists, campaigns and calendar events. **All three are content and theme on machinery that already exists — which is the definition of a register.** Only fitness failed to decompose, and structurally: it cannot mint embers without breaking the second-party rule. **Kept because the identity-inheritance argument that carried the original call was genuinely good and will regenerate.**
- ~~**"Sparks" as a Forge-local currency**~~ — **MOOT.** Option A has no currency at all.
- ~~**Four charters with three of them stubbed**~~ — **CUT.** Writing law for areas that turned out to be themes is fan fiction in a canonical doc.
- ~~**The vocabulary noun "layer"**~~ — **RETIRED.**
- ~~**Renaming `enabled_modules` → `enabled_layers`**~~ — **REVERSED BEFORE IT WAS ACTED ON.**
- ~~**The Forge / FORGE naming collision as an open decision**~~ — **RESOLVED, not by renaming anything.**
- ~~**Porting Endure's systems into Emberhold**~~ — **CUT ON A PLATFORM WALL, NOT A PREFERENCE.**

**2026-07-25 (early)**
- ~~**Emberforge as a standalone sibling app**~~ — **KILLED.** The diagnosis was PROPORTION, not family resemblance: in Emberhold the useful tool is the product and the game is a thin skin; the Forge had grown a tool wall and a metal system while still unable to log a working set.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.**
- ~~**"Emberfit" / "Emberforge" / "Embermill" / "Emberworks" / "Ironhold" as product names**~~ — **MOOT.**
- ~~**Reverting the Lovable project**~~ — **REJECTED ON EVIDENCE.** Version history restores code only; it does not roll back applied migrations and it removes migration files while leaving their objects live. **Forward migration instead. A standing rule.**
- ~~**Loot rolls, salvage, and a Resources currency (from Endure)**~~ — **CUT.**
- ~~**The Hold / clans / blueprints / five construction phases (from Endure)**~~ — **CUT.**

**2026-07-22**
- ~~**"Match Wanmine's feature breadth"**~~ — **REJECTED ON SIGHT.** Their business gets paid at checkout and has **zero incentive to solve day 8**; Emberhold's business is *only* day 8.
- ~~**"Capacitor / app-store path"**~~ — **DECLINED** with two named reopen triggers.
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED BY EVIDENCE.** **Nobody discovers a free lifestyle app by browsing.**

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED AS A VERIFICATION METHOD.** *A scan is a sweep, never a proof.*
- ~~**"Fix the SECURITY DEFINER findings with the panel's 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.**

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG; KILLED.**
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS EVER WRITTEN.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.** ⚠️ *07-26: that call was correct for APPROVED quests and blind to unapproved ones. Two weeklies are stranded on the live board because of it. See NOW.*

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED** by *living-hold ambient themes*.

**2026-07-18 (evening)**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

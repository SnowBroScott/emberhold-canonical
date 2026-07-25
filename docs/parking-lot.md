# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-25 (late).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-25 late)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 THE TRAINING MODULE'S SHAPE — Option A vs Option B (NEW 07-25 late).** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** A clean utility: set logging, automatic programming, rest timing, progressive adjustment, the reason string. No game, no currency, no collection. Writes presence to the activity feed and nothing else. **Buildable now. Cancels a $15.99/mo subscription this month.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence of what was actually trained. No fungible currency — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.
  - **What B owes before it can be written as truth:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? — typing by modality contradicts a LOCKED call in `endure-canonical` that all modalities must produce equal value, and with one available modality there's nothing to type across anyway. (3) does a collection survive the calibration check — *this is a family chore game with your kids' faces in it*?
  - **The standing risk, named because it already happened once:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **🔴 `north-star.md` fold — jAIne has asked three times and 07-25 made it unavoidable (SHARPENED).** It is the doc that says *"read this first, cold,"* and it now describes a product that no longer exists: no constitution, no module architecture, no awareness that two of its own stated principles were rewritten. **It also still owes the success definition and the $636 target** — the gate ladder states a sequence and no destination. **The blocker is protocol, not content:** north-star is not one of the four close-out deliverables, and jAIne will not invent a fifth container without the call. **Two options: (a) add north-star as a fifth full-replace file at close, or (b) a dedicated pass. Pick one. Do not let this sit a fourth time.**
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** (1) the `adults_only` rewards audience read, (2) the `parents_only` quest details read, (3) the ignored scanner finding "adult PIN lock isn't tied to real permission checks," and (4) the ignored finding "any member can submit a redemption attributed to another member." Items 3 and 4 are marked **Ignored** in the Lovable dashboard — **and that panel will never tell you they're conditional. If this fork is ever decided toward per-member auth, two dismissed findings come back to life as real work.** *(A fifth dependent: whether a training module ever serves paying clients rather than hearthmates is the same question wearing a different hat.)*
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU, priced $25 on 07-22). **A one-time SKU funds a one-time year.** Hit the $636 target and year two needs 27 *more* new households, forever. The catalog reframe is the answer — a retained household becomes worth $25 + $10 + $5. **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.** *(07-25 late adds a sharper test for what belongs in a catalog — see LATER #1.)*
- **Quest Log's missing `due_date` filter — by design or by omission?** It shows every quest with no gate, so it displays future-dated successors the board correctly hides. Arguably correct for an oversight surface, arguably a leak. **Decide deliberately.** If it stays unfiltered, consider a visual marker on not-yet-due instances rather than silence.
- **Module navigation (NEW 07-25 late).** Seven tabs is already one past mobile comfort, and an enabled module needs somewhere to live. **It is not "add another tab."** Unresolved and it blocks nothing until a module actually ships.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it. *(The best argument yet: eight tables and two functions were created in production by accident and had to be dropped in production. A staging DB would have made that a non-event.)*
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** (a) a permission tier *inside* a hold, above parent — or (b) a cross-hold super-admin for tier-2 support? Different builds. **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both one-line data changes, no build. ⚠️ **`authenticated` and `anon` are both now correctly denied UPDATE on `is_founder`; the grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 The table-grant read — THE TOP SECURITY ITEM.** One accidental sample (`families`) came back with `anon` holding full CRUD including UPDATE on `is_founder` — the column LOCKED service-role-only on 07-19. **Read every table's grants for `anon` and `authenticated`; revoke anything unearned.** Tables haven't been read since the 07-16 outage, and that outage was the *opposite* failure (grants too narrow). **Both directions are live.**
- **Pip onboarding + empty-board doorway + module intent + timezone — one build, four payloads.** (1) the activation doorway to a first quest, (2) household timezone capture (`Intl.DateTimeFormat().resolvedOptions().timeZone` — Gate B), (3) module intent, (4) the hold-profile screen that makes enablement reversible. **`/setup/intent` already exists unrouted and is the seed of (3)** — do not rebuild it, absorb it. **Design constraint: intent tailors, it never branches permanently.** Ship two options (Household, Training); **do not offer modules that don't exist.**
- **The Clean Toys discriminator (30 seconds, still not done).** Expand it in the Quest Log and read its due date. Future-dated = debris; today-or-earlier = a fourth activeness surface and a partially-untrue LOCKED decision.
- **The overnight rollover check.** First board load of the morning: yesterday's daily gone, a fresh one open and dated today. **The failure mode is silence, not a wrong date.**
- **Monday 2026-07-27 — the recurring reappearance check.** Not a build; just open the board and look. **Two payloads on one test** — if it fails there are two candidates, not one.
- **Grant-revoke verification probe job.** Brief drafted 07-21, deferred. **Add the `families` anon revoke to scope.** The whole job hinges on one distinction: `42501` = broken grant; any app-level error = working grant.
- **Eyeball `/setup/intent`.** It has never been loaded by a human. **HTTP 200 on `/board` is not a render check for a different route.**
- **The floor — avatar render fallback (🟡 glass-verify pending).** Verify on the glass; **check the wall.**
- **Founder tier-tag verification (needs a gate-on moment).** Flip gate ON → confirm 32 lock / 16 open → flip back OFF.

---

## NEXT (soon — off the critical path)

### The training module — Option A

*The best ROI item on the board and it blocks nothing. Fitbod at $15.99/mo is ~$192/yr against a $636/yr break-even target — roughly a third of break-even recovered with no paywall, no funnel, and no Gate E dependency.*

- **Build from scratch against `families` / `profiles` — not by resurrection.** The 07-25 prototype was dropped and that was correct; it was built on an invented parallel tenancy. The rebuild reuses **no schema** and **one artifact**: `src/lib/progression.ts` + its ten tests, which survived deliberately because they are pure, table-agnostic, and have no Supabase imports. **Household-scoped via `current_family_id()`, members read from the existing hearthmate table, avatars via the existing portrait component. A module never mints a person.**
- **The functional core, in build order** (from the market read — Fitbod $15.99/mo, Hevy/Strong free loggers, Boostcamp's 11k free programs; the logger is commoditized and the *hybrid* gap is real): (1) offline-first logging, (2) set entry that survives a sweaty one-handed rest period, (3) RPE/RIR on every set, (4) e1RM trend + weekly volume, (5) rest timing, (6) template-driven program/block authoring.
- **The differentiator is explainability, not AI.** Deterministic autoregulation off RPE, surfacing a one-sentence reason (*"all sets clean under target — adding 10 lb"*). Fitbod's core complaint is a black box making decisions you can't interrogate. **The reason string is the product.**
- **Free/paid is LOCKED and narrower than it first looked.** Deterministic autoregulation, template-driven auto-programming, rest timing and progressive adjustment are **FREE**. **Only LLM-generated weekly programming is PAID** — the sole feature with a real per-user marginal cost. *(The guardrail: paywalling a rest timer would be indefensible.)*
- **Progression logic never lives in Postgres.** A garage has unreliable connectivity; if the rule lives in the database the app is useless the moment wifi drops. Supabase is the sync target, never the compute.
- **The garage wall is an *input* device, not a display.** Emberhold's kitchen wall is glanced at from across the room; the garage tablet is at arm's length mid-rest and is a *better* logging surface than a phone. Big rest timer, plate math readable from the rack, big set-entry tiles. **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Multi-member concurrent sessions are a design requirement, not an edge case.** Scott and May train simultaneously. **Rest timer is derived (`now − last set.logged_at`), never stored** — that alone makes concurrency nearly free. **Attribution, not concurrency, is the real risk:** make the tap the selection (touch her card, log the set) rather than a persistent "current user" mode that goes stale mid-session.
- **No PIN on set logging.** Friction mid-workout is the enemy and the stakes are nil. PIN gates settings and deletions, same as Emberhold.
- **Participation is a per-member flag, not a role check** — when it's needed. "Adults only" is a proxy for "trains" and a twelve-year-old lifting in a garage gym is normal. **Do not build it now.**

### Option B ideas — parked until A has been used for a month

*Captured so they aren't rediscovered, and fenced so they aren't built first.*

- **The tool wall** — a collection of forged implements whose material composition is a readout of which modalities you actually trained. **Hybrid recognition for free, with no classification logic** — you can't forge the thing that needs three materials without having trained three ways.
- **Component-level chronology** — an item built piece by piece, each piece taking the material of what you trained while it was forged. Renderable as finite art composited combinatorially, and **un-gameable because the material is earned before you see it.**
- **Item aging** — a hammer used 200 times shouldn't look new. Patina as a record of work, which kills completionism and ports the heat ramp down to the item.
- **The shared-barbell plate delta** — *"strip 2×25, add 2×45"* when two people alternate at different loads. **Nobody builds this because nobody assumes two people on one bar.** Arguably belongs in Option A, not B — it's a tool feature wearing a game's clothes.
- **The pure/mixed trap, flagged because it's an incentive bug not an art bug:** if a single-material item reads as cleaner or more complete than a mixed one, the app punishes conditioning. **Mixed and pure must be different-beautiful, never ranked.**

### Endure — the separate product

- **🅿️ Endure is parked, not killed, and the block is platform.** Its fitness bridge requires Apple Health / Google Fit with heart rate as gatekeeper and validator. **There is no HealthKit web API**, and the PWA posture is LOCKED (Capacitor DECLINED 07-22). Strider, Unbroken and most of Mystic are therefore unmeasurable in a PWA — not hard, **unavailable**. The hybrid layer (the largest locked doc in `endure-canonical`) is dead on arrival in Emberhold because hybrids require overlap across two paths, and one path has no overlap.
- **Reopen trigger: the same two that reopen Capacitor** — PWA push proving load-bearing at Gate E, or an appliance play after Gate E returns retention driven by the wall. **Endure does not create a third trigger and should not be used to argue for native on its own.**
- **What already ported, principles only:** never decays / absence never punished / no failure states (**Emberhold already does this independently**) · recognition not reward · *"destroying loot is not failure, it is authorship"* · **expression vs. marks**. See LATER #1 for where the fourth one landed.

### Everything else

- **⚠️ Calendar import — a Gate-E thesis risk, not a backlog nicety.** Competitive scan of **Wanmine** shows two-way Google/iCloud/Outlook/Yahoo sync **plus TeamSnap and GameChanger** as table stakes in the ambient-family-display category. Emberhold's calendar is a closed loop, and the calendar is one of the two backbones. **The thirteen existing accounts will never surface this — they're friends, they'll re-key. A cold family with a full Google Calendar will not.** Import (read-only, theirs → ours) is the cheap 80%. **Decide which before Gate D**, because the landing page makes a promise either way.
- **Activity-log actor forgery.** `append_activity` takes `_actor_label text` from the client. Derive server-side from `auth.uid()`. **The activity log is the receipt surface**, and a forged actor corrupts the record an adult would use to catch anything else.
- **Ask Lovable what `sandbox_exec` is.** A role holding EXECUTE on every function in `public`. Pre-existing, so not drift — almost certainly platform infrastructure. **One question, not a project.**
- **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`. One-row reconcile.
- **Test-quest cleanup.** The 07-21 weeklies and the 07-22/23 pre-fix dailies — fold into the broader prod test-object cleanup in `status.md`.
- **"Forgot PIN" confirm() copy.** Claims "only the account owner" — inaccurate.
- **Haiku sweep (queued, read-only):** grep repo for `Feast` → swap any user-facing hit to `Hall` (**confirmed live on the member editor 07-18**); grep every LOCKED decision against the codebase (the "did it land" cross-check); retire/rebuild `avatar-review.tsx`.
- **Re-forge reach across the 13.** Only the logging-in member is prompted. Options: prompt each member on next active session, or a hold-owner "N hearthmates need re-forging" nudge.
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Anchors invisible. Cheap conditional render.
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model. Build phone-first; the wall inherits it.
- **Wall Vault empty-state — two-case branch once persistent favorites ship.**
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward** — the menu floors at 25.
- **Wall event-pill member color — dot-vs-full-tint.** **Wall ticker speed.**
- **Multi-day calendar events (QA #7).** **Calendar alerts (QA #6)** — decide push-vs-in-app before building.
- **Remaining polish burn-down** — Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream, opened 2026-07-22*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Cosmetics only aid *acquisition* where a non-customer can see them. Emberhold cosmetics live on private phones. **The wall is the sole exception** — the only semi-public Emberhold artifact.

**A sharper test than "delight vs. function" (NEW 07-25 late, borrowed from Endure): is this thing a CHOICE or a MEMORY?** Expression is choice — always free, always reversible, outcome-neutral. A **mark** is memory — earned through something that actually happened, and what is paid for is the *ritual of permanence*, not the thing itself. Themes are choice. An artifact produced by six months of real effort is a memory. **This gives the catalog a test that doesn't collapse into "would people pay for it."**

**Hard constraint:** all of it is downstream of day-8. A catalog amplifies retention that exists; it cannot manufacture retention that doesn't.

**#1 — Living-hold ambient themes (SKU #2; household-level, cosmetic, delight-not-access).** Each register as a *living, moving hold* rather than a recolor. **Free = the hearth** (belonging; steady even glow, stays a full tool). **Forge = heat/drive** (bellows, rising embers, hotter pulse). **Hall = revelry** (lantern flicker, drifting warm light). **Garden = growth** (slow sway, pollen, fireflies). **Keep = restraint** (alive by being the calmest — deliberate motion, NOT static).
  - **Register is the aesthetic *source*, never the entitlement.** A hold runs mixed registers.
  - **All themes modulate the ember-lit floor, never replace it.**
  - **Canvas particles YES, bespoke video NO.** Take the canvas harness; leave the MP4 loops.
  - **Kiosk-hardening is a hard requirement, not polish** — `prefers-reduced-motion`, visibility-pause, frame throttling, static fallback. A *scope-shaper*.
  - **Build order de-risks itself: Keep first.** Lowest particle count, doubles as the performance proving-ground.
  - ✅ *The "Forge theme vs Forge module" ambiguity is RESOLVED — registers are aesthetic-only and the module is `training`. No collision remains.*

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow already exists on disk with zero marginal authoring labor. Related: the seasonal frost set.

**#3 — Borders, frames, phone-only flourishes.** Sell only to people already inside. Build last or never.

---

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions, so every cold session starts from the best footing. **07-25 supplied the sharpest entries yet, across both sessions:** jAIne produced a full schema and two Lovable prompts *without fetching a single canonical doc*; invented a third name for an existing tenant; **ratified Lovable's explicit DO-NOT-BUILD violation as intent**; and then wrote an entire master-spec around a four-layer structure that dissolved under one decomposition pass Scott ran himself. The profile should encode: **fetch before producing · decompose before you promote · read what landed against what was briefed · and jAIne's strategic takes are inputs to be argued with, not conclusions.** *Scott has now overruled jAIne on the naming call, the module-merge call, the currency call, and the layer-collapse call — and was right all four times.*
- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The real distinction is approver vs. submitter. A display-string sweep, not a data migration.
- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** Still fenced for the STRANGERS-grade version: wall redemption/spend commit (not yet scoped), deployment gremlins (Safari, PWA display-mode, screen-sleep — *don't over-tune to the borrowed iPad*), P4×L8 on the wall's write surface, favorites on the wall.
- **Seed distribution channels — pick one or two, never all.** **The highest-leverage lever is not a channel — it's the invite/deep-link loop.** Then: one earnest post in r/daddit / r/Mommit / r/homeschool; Product Hunt + PWA directories; Habitica's existing audience; **the homeschool angle (🔵 VALIDATED 07-10 — came from outside Scott's head, which makes it worth more).** **Don't become a content marketer.** *(27 strangers over twelve months is roughly one every other week.)*
- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log. Quality's consumer lives here.
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer.

---

## KILLED / SUPERSEDED

**2026-07-25 (late)**
- ~~**The four registers as four functional layers (Forge / Keep / Garden / Hall)**~~ — **SUPERSEDED ONE DAY AFTER IT WAS LOCKED, by decomposition.** Keep (upkeep, chores, repair, DIY) is quests. Garden (yard, planting, outdoor maintenance) is quests. Hall (celebration, hosting, parties, feasting) is lists, campaigns and calendar events. **All three are content and theme on machinery that already exists — which is the definition of a register.** Only training failed to decompose, and structurally: it cannot mint embers without breaking the second-party rule. **Kept here because the identity-inheritance argument that carried the original call was genuinely good and will regenerate** — the four registers ARE paid-for identity work (four palettes, four icons, four ambient themes, 48 avatars, a live `members.class` enum). They keep every bit of that job. **They just don't also run the household.**
- ~~**"Sparks" as a Forge-local currency**~~ — **MOOT.** Option A has no currency at all. The architecture question it raised was real and got answered as a constitutional rule (**no conversion**); the naming exercise was never the decision.
- ~~**Four charters with three of them stubbed**~~ — **CUT.** Writing law for areas that turned out to be themes is fan fiction in a canonical doc, and a stub invites someone to fill it.
- ~~**The vocabulary noun "layer"**~~ — **RETIRED.** Coined to name four functional areas; three were registers and one is a module. An extra structural noun that buys nothing is exactly the drift the string law exists to prevent.
- ~~**Renaming `enabled_modules` → `enabled_layers`**~~ — **REVERSED BEFORE IT WAS ACTED ON.** The recommendation was predicated on four layers and evaporated with them. **The column is correctly named as shipped: `['household']` = base, `['household','training']` = base + training. Do not migrate it.**
- ~~**The Forge / FORGE naming collision as an open decision**~~ — **RESOLVED, not by renaming anything.** A register is aesthetic-only and gates nothing; the module's stored string is `training`. Defining the two nouns dissolved the overload.
- ~~**Porting Endure's systems into Emberhold**~~ — **CUT ON A PLATFORM WALL, NOT A PREFERENCE.** Loot tables, blueprints, ten identity pools, clans, five construction phases, drift ceremonies, aspect states. **The bridge requires HealthKit; there is no HealthKit web API; the PWA posture is LOCKED.** Self-logged strength is the only modality a PWA can honestly measure. **Principles ported; systems did not. Endure itself is parked, not killed — see NEXT.**

**2026-07-25 (early)**
- ~~**Emberforge as a standalone sibling app with its own domain, tenancy, and users**~~ — **KILLED, and the reasoning matters more than the conclusion.** It got as far as a purchased-domain recommendation (`theemberforge.com`), a full eight-table schema, and a working vertical slice before Scott's wife independently identified from the outside that it no longer felt like part of Emberhold. **The diagnosis she was reaching for was PROPORTION, not family resemblance** — in Emberhold the useful tool is the product and the game is a thin skin; the Forge had grown a tool wall, a metal system, and project-scoped blocks while still being unable to log a working set. Kept here because a separate-app frame is the intuitive default for a second product and it will regenerate if it isn't written down as decided.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.** A third name for a tenant that is already `families`, invented by jAIne from memory without fetching a doc. **The tell was available in thirty seconds and nobody looked for it.** Zero rows survived.
- ~~**"Emberfit" / "Emberforge" / "Embermill" / "Emberworks" / "Ironhold" as product names**~~ — **MOOT.** The training layer is a module, not a product.
- ~~**Reverting the Lovable project to before the 07-25 work**~~ — **CONSIDERED AND REJECTED ON EVIDENCE.** Version history restores code only. It does not roll back applied migrations, and it **removes migration files while leaving their objects live** — manufacturing exactly the schema-history drift this repo exists to prevent. **Forward migration instead. A standing rule, not a one-off call.**
- ~~**Loot rolls, salvage, and a Resources currency (from Endure)**~~ — **CUT.** "XP is guaranteed and never decays" and "loot is a roll" are opposite promises made in the same document; two identical sessions paying differently is the wrong message for a product about effort compounding, and slot-machine mechanics attached to exercise is a bad look from a trainer.
- ~~**The Hold / clans / blueprints / five construction phases (from Endure)**~~ — **CUT as a second full game with zero connection to whether you trained today.** *(Also a hard noun collision: Endure's Hold is a built settlement; Emberhold's Hold is the tenant.)*

**2026-07-22**
- ~~**"Match Wanmine's feature breadth — meal planner, home inventory, photo stream, message board"**~~ — **REJECTED ON SIGHT.** Four modules with no game in them. Their business gets paid at checkout and has **zero incentive to solve day 8**; Emberhold's business is *only* day 8. *(07-25 late note: the module architecture is now much smaller than this and the caveat is no longer needed — there is one optional module, not four.)*
- ~~**"Capacitor / app-store path — assessed viable"**~~ — **DECLINED** with two named reopen triggers (PWA push proving load-bearing at Gate E; or an appliance play after Gate E returns retention driven by the wall). *(07-25 late: Endure's HealthKit requirement does NOT constitute a third trigger.)*
- ~~**"The app store is a distribution channel"**~~ — **FALSIFIED BY EVIDENCE.** Wanmine: ~172 iOS ratings and ~1.8k Android installs in ten months, with hardware behind it. **Nobody discovers a free lifestyle app by browsing.**

**2026-07-21 (late)**
- ~~**"Re-run the deep security scan to verify the grant fix"**~~ — **REJECTED AS A VERIFICATION METHOD.** A static scanner re-runs rules; it cannot confirm a grant landed or a live path still works. *A scan is a sweep, never a proof.*
- ~~**"Fix the SECURITY DEFINER findings with the panel's 'Try to fix all (free)' button"**~~ — **NEVER ATTEMPTED, DELIBERATELY.** Five of ten findings are load-bearing behavior.

**2026-07-21 (evening)**
- ~~**"The Quick Add favorite chip silently auto-assigns the assignee"**~~ — **NEVER A REAL BUG; KILLED.** *(Root failure: jAIne inferred user intent from a stray recon line instead of believing Scott's stated deliberate action.)*
- ~~**"`due_date` is not part of activeness"**~~ — **SUPERSEDED BEFORE IT WAS EVER WRITTEN.**
- ~~**A client-invoked roll-forward job for weekly/monthly**~~ — **SCOPED THEN CUT AS REDUNDANT.**

**2026-07-19 (morning)**
- ~~**Theme packs as palette swaps / recolors**~~ — **SUPERSEDED** by *living-hold ambient themes*.

**2026-07-18 (evening)**
- ~~**"What happens to the thirteen existing `hero:` picks"**~~ — **RESOLVED.** Re-forge shipped.
- ~~**"The free/paid avatar split — arithmetic is stale"**~~ — **RESOLVED at 16/32.**
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design.**
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** Close = four named deliverables in four fixed formats, no wrapper.

**2026-07-18 (day)**
- ~~Identity-bound wall commit~~ — walked back to adult-verify.
- ~~SnowDad Vault spendable = 0 as a bug~~ — resolved as test cruft.
- ~~"Scott sets every status" doc rule + compliance checkpoint~~ — discarded, never canon.
- ~~Automated / hand-cut avatar circles~~ — superseded by fill-corners; now moot.

**2026-07-17**
- ~~Wall calendar event detail / class-color pills / realtime data / Rewards accordion~~ — shipped-or-corrected. Wall interaction layer — BUILT.

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, "all quiet" resolved, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, auth email promoted, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

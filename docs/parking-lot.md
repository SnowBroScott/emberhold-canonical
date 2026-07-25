# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-25.*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-25)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 THE FORGE'S CURRENCY — collides head-on with a LOCKED principle (NEW 07-25).** Master-spec LOCKS *"Two backbones: one currency (embers) and one timeline (the calendar) — every feature is a source, sink, or pool."* A Forge-local currency ("sparks") is not a dialect of that rule; it is a second backbone. **Two real options, and they build differently:**
  - **(a) Training mints embers.** Zero amendment, one economy, immediately consistent with everything shipped. Cost: a workout and a chore pay the same currency, which may flatten both. Also raises whether training embers should be spendable in the Vault — if yes, the household economy now has a source nobody approves.
  - **(b) Amend two-backbones to permit sealed per-module economies.** Each module gets its own currency, **never convertible** between modules (the moment sparks buy seeds there is an exchange rate and a farming exploit). Cost: amends a load-bearing principle, and every future "how many X is a Y" question becomes live.
  - **The naming was the fun part and it is not the decision.** Sparks / seeds / shards / salt are good and cheap; the architecture underneath them is neither. **Decide (a) or (b) before any Forge build touches an economy.**
- **🔴 The three 07-25 amendments are `proposed LOCKED` — Scott sets status (NEW 07-25).** The monetization clause (*"free is a full tool; paid is delight, **or marginal cost**"*), the membrane clause (*"each module may carry at most one game layer, and the layers are sealed"*), and the module-tenancy call. **Until status is set, the principles in `master-spec.md` govern as written.** Full reasoning in `decisions.md` 07-25.
- **🔴 The Forge / FORGE naming collision (NEW 07-25).** `members.class` already carries `FORGE` as an avatar and animation-theme value. A *module* also called the Forge is ambiguous in the same app and the same schema. `enabled_modules` deliberately stores the neutral string `training` so nothing is hard-coded. **Options:** rename the module, rename the class, or accept the overload with a strict rule about which surfaces use which. **Decide before it ships anywhere a user can read it.**
- **⚠️ Own-session vs per-member-auth — now has more riding on it than it looks.** **Four separate open items wait on this one call:** (1) the `adults_only` rewards audience read, (2) the `parents_only` quest details read, (3) the ignored scanner finding "adult PIN lock isn't tied to real permission checks," and (4) the ignored finding "any member can submit a redemption attributed to another member." Items 3 and 4 are currently marked **Ignored** in the Lovable dashboard — and that panel will never tell you they're conditional. **If this fork is ever decided toward per-member auth, two dismissed findings come back to life as real work.** *(07-25 adds a fifth dependent: whether a training module ever serves paying clients rather than hearthmates is the same question wearing a different hat — a client is not a hearthmate and cannot share a household tenant.)*
- **⚠️ `north-star.md` fold — jAIne asked twice, Scott hasn't called it, and 07-25 made it worse (SHARPENED 07-25).** The success definition and the $636 target belong in north-star: the gate ladder currently states a sequence and no destination. **07-25 added a second thing north-star can't currently hold — Emberhold is now a four-module product on paper, and the ladder describes a one-module product.** **The blocker is protocol, not content** — north-star is not one of the four close-out deliverables, and jAIne will not invent a fifth container without the call. Two options: (a) add north-star as a fifth full-replace file at close, or (b) leave it decisions-only and let north-star catch up in a dedicated pass alongside the master-spec fold. **Do not let this sit.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is a one-time household unlock (LOCKED as v1 SKU, priced $25 on 07-22). **A one-time SKU funds a one-time year.** Hit the $636 target and year two needs 27 *more* new households, forever. The catalog reframe is the answer — a retained household becomes worth $25 + $10 + $5. **The hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.** *(07-25: modules are a third possible answer nobody has priced. A household that runs three modules is stickier than one that runs one — but per the amended clause, modules themselves must stay free tools. Only their delight and their marginal-cost features can be sold.)*
- **Quest Log's missing `due_date` filter — by design or by omission?** Quest Log shows every quest with no `due_date` gate, so it displays future-dated recurring successors the board correctly hides. Arguably correct for an oversight surface, arguably a leak. **Decide deliberately.** If it stays unfiltered, consider a visual marker on not-yet-due instances rather than silence.
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it. *(07-25 is the best argument yet: eight tables and two functions were created in production by accident and had to be dropped in production. A staging DB would have made that a non-event.)*
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** (a) a permission tier *inside* a hold, above parent — or (b) a cross-hold super-admin for tier-2 support? Different builds. **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both one-line data changes, no build. **⚠️ 07-25 note:** `authenticated` and `anon` are both now correctly denied UPDATE on `is_founder`; the grandfather write must run as `service_role`.
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder — PARTIALLY RESOLVED 2026-07-17.** The unproven half is answered (built for own-hold, it works). What's fenced is the distribution-grade version. *(07-25: the wall gained a second job — the garage tablet. See NEXT.)*
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 The table-grant read — RE-PRICED, NOW THE TOP SECURITY ITEM (07-25).** Was "cheap, non-blocking." One accidental sample (`families`) came back with `anon` holding full CRUD including UPDATE on `is_founder` — the column LOCKED service-role-only on 07-19. **Read every table's grants for `anon` and `authenticated`; revoke anything unearned.** Tables haven't been read since the 07-16 outage, and that outage was the *opposite* failure (grants too narrow). Both directions are live.
- **Pip onboarding + empty-board doorway + module intent — now one build, three payloads (07-25).** Scott's framing: *onboarding will need to take into account the potential/likely other layers.* The three: (1) the activation doorway to a first quest, (2) household timezone capture (`Intl.DateTimeFormat().resolvedOptions().timeZone` — Gate B), (3) module intent. **`/setup/intent` already exists unrouted and is the seed of (3)** — do not rebuild it, absorb it. **Design constraint: intent tailors, it never branches permanently.** Ship two options (Household, Training); do not offer modules that don't exist.
- **The Clean Toys discriminator (carried from 07-23, 30 seconds, still not done).** Expand it in the Quest Log and read its due date. Future-dated = debris; today-or-earlier = a fourth activeness surface and a partially-untrue LOCKED decision.
- **The overnight rollover check (carried from 07-23).** First board load of the morning: yesterday's daily gone, a fresh one open and dated today. **The failure mode is silence, not a wrong date.**
- **Grant-revoke verification probe job.** Brief drafted 07-21, deferred. **Add the 07-25 `families` anon revoke to scope.** The whole job hinges on one distinction: `42501` = broken grant; any app-level error = working grant.
- **`master-spec.md` fold — now owes FOUR things.** (1) the recurring-quest lifecycle, (2) the activeness model, (3) the Founding Guildhall price + success definition, (4) **NEW — the module architecture and whichever 07-25 amendments Scott locks.** **A short dedicated session** — read the current spec, fold all four, full-replace. Deliberately *not* regenerated blind at close, three times now. **This is the oldest debt on the board and it is compounding.**
- **Monday 2026-07-27 — the recurring reappearance check.** Not a build; just open the board and look.
- **The floor — avatar render fallback (🟡 glass-verify pending).** Verify on the glass; check the wall.
- **Founder tier-tag verification (needs a gate-on moment).** Flip gate ON → confirm 32 lock / 16 open → flip back OFF.
- **Eyeball `/setup/intent` (NEW 07-25).** It has never been loaded by a human. HTTP 200 on `/board` is not a render check for a different route.

---

## NEXT (soon — off the critical path)

### The Forge module (the first child layer)

- **Rebuild the training slice against `families` / `profiles` — from scratch, not by resurrection (NEW 07-25).** The 07-25 prototype was dropped and that was correct; it was built on an invented parallel tenancy. The rebuild reuses **no schema** and **one artifact**: `src/lib/progression.ts` + its ten tests, which survived deliberately because they are pure, table-agnostic, and have no Supabase imports. **Household-scoped via `current_family_id()`, members read from the existing hearthmate table, avatars via the existing portrait component. The Forge never mints a person.**
- **The functional core, in build order** (from the 07-25 market read — Fitbod $15.99/mo, Hevy/Strong free loggers, Boostcamp's 11k free programs; the logger is commoditized and the *hybrid* gap is real): (1) offline-first logging, (2) set entry that survives a sweaty one-handed rest period, (3) RPE/RIR on every set, (4) e1RM trend + weekly volume, (5) cross-modality load, (6) program/block authoring.
- **The differentiator is explainability, not AI.** Deterministic autoregulation off RPE, surfacing a one-sentence reason ("all sets clean under target — adding 10 lb"). Fitbod's core complaint is a black box making decisions you can't interrogate. **The reason string is the product.**
- **Weekly-cadence AI programming as the paid tier.** LLM plans the week with stated rationale; deterministic rules run the session. One call per user per week is pennies; per-session AI is where the unit economics die. **Depends on the amended monetization clause — do not build until it's LOCKED.**
- **The garage wall is an *input* device, not a display.** Emberhold's kitchen wall is glanced at from across the room; the garage tablet is at arm's length mid-rest and is a *better* logging surface than a phone. Big rest timer, plate math readable from the rack, big set-entry tiles. **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**
- **Multi-member concurrent sessions are a design requirement, not an edge case.** Scott and May train simultaneously. **Rest timer is derived (`now − last set.logged_at`), never stored** — that alone makes concurrency nearly free. **Attribution, not concurrency, is the real risk:** make the tap the selection (touch her card, log the set) rather than a persistent "current user" mode that goes stale mid-session.
- **No PIN on set logging.** Friction mid-workout is the enemy and the stakes are nil. PIN gates settings and deletions, same as Emberhold.
- **Parked Forge ideas, captured so they aren't rediscovered:** the **tool wall** (a collection of forged implements whose metal composition is a readout of which modalities you actually trained — hybrid recognition for free, no classification logic); **component-level chronology** (an item built piece by piece, each piece taking the metal of what you trained while it was forged — renderable as finite art composited combinatorially, and un-gameable because the metal is earned before you see it); **item aging** (a hammer used 200 times shouldn't look new — patina as a record of work, which kills completionism and ports the heat ramp down to the item); and the **shared-barbell plate delta** ("strip 2×25, add 2×45" when two people alternate at different loads — nobody builds it because nobody assumes two people on one bar). **All downstream of a working log. None of it before.**
- **The pure/mixed trap, flagged now because it's an incentive bug not an art bug:** if a single-metal item reads as cleaner or more complete than a mixed one, the app punishes conditioning. **Mixed and pure must be different-beautiful, never ranked.**

### The three downstream modules (Keep / Garden / Hall)

- **Not scoped, not committed, and deliberately not offered in onboarding until they exist.** The mapping, from 07-25: **KEEP** = the house maintained — DIY, upkeep, home systems, records (Scott's correction: for a homeowner, "DIY projects" and "maintenance" are nearly the same set; recreational woodworking is the edge case, not the center). **GARDEN** = growing — plants, herbs, beds, seasons. **HALL** = cooking and feeding — recipes, meal planning, shopping.
- **The structural note that decides how they get built:** Forge is the only register about a **person**; the other three are about the **property**. That is exactly why Forge is the right one to build first and standalone-ish, and why Garden/Hall/Keep will sit much closer to Emberhold's existing orbit — Hall in particular overlaps Lists and the calendar heavily and may be mostly a re-view of data that already exists.
- **Module-vs-feature is undecided for all three.** Do not assume four symmetrical builds.

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
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift.

---

## LATER (backlog)

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream, opened 2026-07-22*

**The frame:** the catalog is **leverage on retention succeeding**, not insurance against acquisition failing. A retained household becomes worth $25 + $10 + $5 instead of $25.

**The priority rule — rank by wall-visibility.** Cosmetics only aid *acquisition* where a non-customer can see them. Emberhold cosmetics live on private phones. **The wall is the sole exception** — the only semi-public Emberhold artifact.

**Hard constraint:** all of it is downstream of day-8. A catalog amplifies retention that exists; it cannot manufacture retention that doesn't.

**#1 — Living-hold ambient themes (SKU #2; household-level, cosmetic, delight-not-access).** Each register as a *living, moving hold* rather than a recolor. **Free = the hearth** (belonging; steady even glow, stays a full tool). **Forge = heat/drive** (bellows, rising embers, hotter pulse). **Hall = revelry** (lantern flicker, drifting warm light). **Garden = growth** (slow sway, pollen, fireflies). **Keep = restraint** (alive by being the calmest — deliberate motion, NOT static).
  - **Class is the aesthetic *source*, never the entitlement.** A hold runs mixed classes.
  - **All themes modulate the ember-lit floor, never replace it.**
  - **Canvas particles YES, bespoke video NO.** Take the canvas harness; leave the MP4 loops.
  - **Kiosk-hardening is a hard requirement, not polish** — `prefers-reduced-motion`, visibility-pause, frame throttling, static fallback. A *scope-shaper*.
  - **Build order de-risks itself: Keep first.** Lowest particle count, doubles as the performance proving-ground.
  - *(07-25 note: if modules ship, "Forge theme" becomes ambiguous with "Forge module." Same collision as the class name — resolve once, everywhere.)*

**#2 — Avatar packs / cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow already exists on disk with zero marginal authoring labor. Related: the seasonal frost set.

**#3 — Borders, frames, phone-only flourishes.** Sell only to people already inside. Build last or never.

---

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions, so every cold session starts from the best footing. **07-25 adds the sharpest entries yet:** jAIne produced a full schema and two Lovable prompts *without fetching a single canonical doc*, invented a third name for an existing tenant, and then **ratified Lovable's explicit DO-NOT-BUILD violation as intent**. The profile should encode: fetch before producing, read what landed against what was briefed, and *jAIne's strategic takes are inputs to be argued with, not conclusions* — Scott has now overruled jAIne on the naming call and the module-merge call, and was right both times.
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

**2026-07-25**
- ~~**Emberforge as a standalone sibling app with its own domain, tenancy, and users**~~ — **KILLED, and the reasoning matters more than the conclusion.** It got as far as a purchased-domain recommendation (`theemberforge.com`), a full eight-table schema, and a working vertical slice before Scott's wife independently identified from the outside that it no longer felt like part of Emberhold. **The diagnosis she was reaching for was proportion, not family resemblance** — in Emberhold the useful tool is the product and the game is a thin skin; the Forge had grown a tool wall, a metal system, and project-scoped blocks while still being unable to log a working set. **Superseded by the module architecture.** Kept here because a separate-app frame is the intuitive default for a second product and it will regenerate if it isn't written down as decided.
- ~~**`holds` / `hold_accounts` / `members` as a parallel tenancy**~~ — **BUILT AND DROPPED THE SAME NIGHT.** A third name for a tenant that is already `families`, invented by jAIne from memory without fetching a doc. **The tell was available in thirty seconds and nobody looked for it.** Zero rows survived. See `status.md` 07-25.
- ~~**"Emberfit" / "Emberforge" / "Embermill" / "Emberworks" / "Ironhold" as product names**~~ — **MOOT.** The child apps are modules, not products; the module label question survives as an open decision (see the FORGE collision above), but the product-naming exercise is dead.
- ~~**Reverting the Lovable project to before the 07-25 work**~~ — **CONSIDERED AND REJECTED ON EVIDENCE.** Lovable's version history restores code only. It does not roll back applied migrations, and it **removes migration files while leaving their objects live** — manufacturing exactly the schema-history drift this repo exists to prevent. **Forward migration instead. This is now a standing rule, not a one-off call.**
- ~~**Loot rolls, salvage, and a Resources currency (from the Endure design)**~~ — **CUT.** "XP is guaranteed and never decays" and "loot is a roll" are opposite promises made in the same document; two identical sessions paying differently is the wrong message for a product about effort compounding, and slot-machine mechanics attached to exercise is a bad look from a trainer. Deleting loot makes the economy *simpler*, not poorer. **Retained from Endure:** the four-modality taxonomy, guaranteed non-decaying progress, absence-is-never-punished, and identity emerging from behavior.
- ~~**The Hold / clans / blueprints / five construction phases (from Endure)**~~ — **CUT as a second full game with zero connection to whether you trained today.** Right-sized into the catalog pattern: a cosmetic surface, not a base-builder.

**2026-07-22**
- ~~**"Match Wanmine's feature breadth — meal planner, home inventory, photo stream, message board"**~~ — **REJECTED ON SIGHT.** Four modules with no game in them. Their business gets paid at checkout and has **zero incentive to solve day 8**; Emberhold's business is *only* day 8. *(07-25 caveat worth holding: the module architecture is not this. The distinction is that Keep/Garden/Hall are the four registers Emberhold already had an identity system for, chosen by the household at onboarding and tailored to — not a feature list bolted on to match a competitor.)*
- ~~**"Capacitor / app-store path — assessed viable"**~~ — **DECLINED 2026-07-22** with two named reopen triggers (PWA push proving load-bearing at Gate E; or an appliance play after Gate E returns retention driven by the wall).
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

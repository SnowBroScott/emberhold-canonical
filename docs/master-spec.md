# Master Spec
**What Emberhold IS.** Canonical design truth — above any individual build prompt or chat. Status lives in `status.md`; this doc does not track it.

Last substantive update: **2026-07-25** — the constitution restructure. Platform law separated into a supreme **constitution** that no optional module may contradict. Two constitutional rules rewritten: "one currency" became **no conversion**, and verification-minting was rescoped to **approval authority**. The 07-25 "four registers are four functional layers" reading is **superseded** — see *Registers and modules*. Also folded, all four overdue: the activeness model and recurring lifecycle (07-21), the household-local date rule (07-23), and the 07-25 decision set.

---

## What it is
A gamified household operating system. Household work becomes **quests** worth **embers**; kids claim and complete them, adults approve, and embers drive a leaderboard and real rewards. Built mobile-first as an installable PWA, live at **theemberhold.com**.

> **Vocabulary note.** User-facing labels are neutral: role label is **"Adult"** (not "Parent"), and the group is **"the Hold"** (not "Family"). This lets a hold *with* kids and one *without* (couples, roommates) both fit. The underlying role enum and permission logic are unchanged — neutral wording is a UI-layer relabel, not a model change. (Deeper power-structure flexibility — flat/peer holds — is parked.)

## The thesis (the moat)
**Out-habit, don't out-feature.** Paper calendars and $400 displays (Skylight) win only because they're *ambient*; Google/Apple lose because nothing makes you open them daily, so they drift and need a human admin. Emberhold's game engine is a daily-habit driver — the household's organization stays current as a *side effect of the fun*. We don't beat Google on features; we beat it on habit, and we own the family-specific jobs it does badly.

*(Thesis, gate ladder, and doc-ownership map live in `north-star.md` — read that first, cold.)*

---

## THE VOCABULARY (LOCKED — the string law)
Vocabulary drift is a real failure mode. These are the rules, not suggestions.

**Structural nouns:**
- **The hold** is the tenant — the household and its hearthmates. (`families` in the schema for historical reasons; user-facing it is always the Hold.)
- **A surface** is a platform screen or system every hold gets: the board, the Vault, campaigns, the calendar, Lists, the Briefing, the wall.
- **A module** is an optional functional area a hold may enable. **There is exactly one: fitness.** Stored in `families.enabled_modules`.
- **A register** is one of **Forge · Garden · Keep · Hall**. Registers are **aesthetic only** — an avatar class and an ambient theme. **A register is not a functional area.**
- *Retired: "layer."* Coined to describe four functional areas. Three (Keep, Garden, Hall) decomposed entirely into quests, lists and campaigns on inspection — they were registers all along. With one genuine module left, the extra noun bought nothing.

**The fitness module's names — concept in the data, product on the glass:**
- **`fitness` is the stored value and the concept word.** Universally understood, needs no explanation to a stranger, and describes the domain rather than one activity within it.
- **FORGE is the product name.** It is what appears on screen — the name of the fitness home inside Emberhold.
- **This is the same pattern already in use:** the schema says `families`, the UI says "the Hold."
- *Retired: **"training."*** It was the working word through 07-25 and is superseded by `fitness`. Existing `decisions.md` entries retain it as written — the file is append-only, and a string-law change gets a new entry rather than a retroactive edit.
- **The Forge register and the Forge module share a name deliberately, and it is not a collision.** *"Warriors, protectors, ember-forged heroes"* and the place you go to lift point the same direction — same word, same meaning. (This was flagged as a naming conflict while Forge was a proposed *household* functional area, where the two senses genuinely diverged. That conflict died with the layer model.)

**Currency and game terms:**
- **Embers is the currency of the platform economy. XP is dead.** Campaign progress renders as a bare **%** (earning-type campaigns showing a real ember count are the only exception). The `points` field name is unchanged — a UI-string law, not a schema law.
- **"Quest" is the universal object term.** Every task object is a quest, assigned or not. Open/Assigned **status badges** carry the state — the *word* never changes with it. *(This walks back the earlier `assigned_to`-null-means-"Bounty" rule: it created friction in real use and confused a tester. "Quest" being universally understood is a feature — the differentiation budget is already well spent on ember, hold, hearth, Vault, Ranks.)*
- **"Bounty" survives as a proper noun in exactly one place:** the Briefing's unclaimed-quest strip, **"Open Bounties."** Nowhere else.
- **The tier ramp is DIM → WARM → HOT → BLAZING.** Four tiers, derived from points, driving visual + animation intensity.
- **"Legendary" is NOT a fifth tier.** A **campaign-completion override state** — a one-off significance flag on the feed.
- **Pre-auth "Hold" grammar rule.** Imperative / doorway framing grounds the word and self-teaches it ("Enter the Hold" ✓). Cold possessive use does not ("Your hold's quest board" ✗) — a brand-new user has no referent. Possessive pre-auth copy uses plain language ("household," "home"). Post-auth, "Hold" is free everywhere.

---
---

# PART I — THE CONSTITUTION

Platform law. **Supreme: no module may contradict a rule in this part.** A module may *specialize* within a rule — never carve an exception. If a module genuinely needs an exception, the constitution is wrong and gets amended deliberately in `decisions.md`, once — not per-module, forever.

## The seven constitutional rules

**1. NO CONVERSION.** *(LOCKED 2026-07-25 — replaces "one currency to rule them all.")*
Currencies may be multiple. They may **never** exchange, convert, or purchase across a module boundary. The load-bearing thing was always the absence of an exchange rate, not the absence of plurality — a foreign-exchange desk inside a chore app is the failure mode; two sealed economies sharing a login are not.
- **Standing law with nothing to govern yet, deliberately.** No second currency exists. This is the only moment a rule like this is cheap.
- **Burden of proof for a new currency is high and explicit.** A module earns one only with (a) its own distinct earn, (b) its own distinct sink, and (c) no conversion path. **Miss any one and what it wants is a counter, not a currency.**
- **The semantic reason matters more than the mechanical one:** an ember means *an adult approved this, and it buys me a real thing.* Tight, legible, and what gives the Vault teeth. A second currency exists to keep **embers** clean, not to add a wallet.
- **The seal breaks quietly.** The requests that will arrive — *"can module currency buy a Vault reward?"* and *"can a kid trade it for embers?"* — are DECLINED in advance.

**2. MINTING REQUIRES AN APPROVAL-AUTHORIZED ACTOR. APPROVAL AUTHORITY IS ADULT-ONLY.** *(LOCKED 2026-07-25 — rescopes "embers mint only on adult approval.")*
Embers come into existence only when an actor holding approval authority approves. That authority is adult-only, enforced at the trigger layer by `a_enforce_quest_update_authority`.
- **This is a rule about AUTHORITY, not about party count.** For a kid it necessarily means a second person. For an adult it is satisfied by themselves — adult self-completion writes `status='approved'` + `approved_by=self` atomically, and **the gate is satisfied, not bypassed.** An earlier phrasing of this rule ("a second party must mint") was wrong: it was contradicted by shipped, deliberate behavior.
- **The residual hole is real and deliberately unclosed.** An adult can mint themselves unlimited embers. They would be buying "pick dinner" and "one hour alone" — household-internal favors with no external cost. An adult lying to themselves is not a problem this app should solve.
- **The kid case is where it bites, and it is why the fitness module cannot mint embers.** A kid's ember requires an adult to verify, and **nobody can verify a squat set.** Either the approval gate becomes a rubber stamp — corrupting what approval means everywhere, including chores — or fitness effort does not mint embers. It does not.
- **A sealed, in-app-only currency needs no approval gate at all.** It buys nothing outside the app, so self-logging is self-approval, and the only victim of an inflated count is the squat. Outside the adult gate on kids' embers, **Emberhold does not verify that people did what they said.** We are not the police.

**3. ONE TIMELINE.** The calendar is shared. A workout, a dentist appointment and a party are all events on the same surface. No module ships a second calendar.

**4. THE MONETIZATION SEAM IS HOUSEHOLD-LEVEL, ALWAYS.** Per-class, per-character, per-kid, or per-module-per-kid SKUs recreate a *"buy each kid their skin"* dynamic **inside a household tool** — the worst possible seam between a parent and their kids. One purchase, everyone gets everything. **No exceptions, ever.**

**5. ONE MEMBER TABLE. A MODULE NEVER CREATES A PERSON.** *(LOCKED 2026-07-25.)*
Exactly one member-creation path exists. Modules **read** hearthmates from the existing member table, render them with the **existing hero-portrait component** (so avatars, fallbacks, and identity colors stay consistent), and point at the existing member identifier. An empty state routes to the add-hearthmate flow; it never offers a local shortcut.
- *Earned the hard way: a fitness prototype shipped a free-text "Who's training?" field that minted its own rows. Two ways to make a person produces "Mia" in the hold and "mia" in the Forge with no reconciliation path — duplicated avatars, split history, an identity model nobody can trust.*
- **Deliberately unsolved:** filtering a module's member list by "Adults" is a proxy for "participates," and it will break — a twelve-year-old lifting in a garage gym is normal. When it breaks the answer is a **per-member flag**, not a role check. Not built now.

**6. BRIGHTNESS = HEAT = IMPORTANCE.** The hierarchy principle, the animation-intensity dial, the avatar-luminosity floor, and the Vault's affordability-as-heat mode switch are one rule wearing four hats. Every module and register inherits it.

**7. FREE IS A FULL TOOL. PAID IS DELIGHT, *OR* MARGINAL COST.** *(Amended LOCKED 2026-07-25.)*
The membrane applied to money. Paid may never be access to basic function — **with one generalized carve-out**: a feature with a genuine, unavoidable **per-user marginal cost** may be paid.
- **GUARDRAIL, so the clause doesn't rot:** the cost must be *real and unavoidable, not architected into existence.* Anything can be made to "incur cost" by routing it through an API on purpose. If a feature could run on-device or as a deterministic rule and a model call was added to justify a paywall, that is the rule being gamed against itself. **Paywalling a rest timer would be indefensible.**
- **The live test case:** *LLM-generated* programming is paid. **Deterministic** autoregulation, template-driven auto-programming, rest timing and progressive adjustment are **free** — they run on rules, not tokens.
- Generalizing rather than granting an exception is deliberate: an exception invites the next case to be argued from scratch; a clause gives a test for video hosting, exports, and anything else that bills per user.

---

## The hold (the tenant)
- **Members ("hearthmates"):** name, avatar (hero portrait), role (**Adult / Kid** — display label; role enum unchanged), class (**Forge / Garden / Keep / Hall** — the register).
- **Model:** profile-switching, shared-device first (Netflix-style — tap to switch), because young kids have no phone. Join-code is the *secondary* path. **Kids have no auth identity** — sub-profiles acting under an adult's authenticated session. *(Also the COPPA advantage: no kid email, no direct collection. And the reason naive `auth.uid()`-based RLS fixes don't work — see Security.)*
- **Adult profiles are PIN-locked, fail closed.** A PIN is *mandatory* at adult creation (with confirm-entry); existing PIN-less adults are intercepted and forced to set one. No path into an Adult profile without a PIN. This gives the approval gate teeth (anti-cheat).
- **Adult profile creation** routes through the `create_adult_profile` SECURITY DEFINER RPC.
- **PIN / password recovery (three locks):** (a) **adult-resets-adult** — a reset (↺) on each other adult's member card clears their PIN; fail-closed intercept forces a new one on switch-in. (b) **account-owner master path** — "Forgot PIN?" clears that profile's PIN via the owner-gated `set_profile_pin` RPC; closes the sole-adult lockout. (c) **account login** — "Forgot password?" → Supabase built-in reset → `/reset-password`. No new auth system, no new tables. *(Threat note: an acting adult can reset another adult's PIN — peer trust, fine for a 2-adult hold; revisit for peer/roommate holds.)*
- **Hold membership:** an owner; an editable hold name. **Invite to the Hold** fires the Web Share API with a prefilled message + deep link `theemberhold.com/join?code=XXXX` (desktop clipboard fallback). **Join code visibility + invite are Adult-only.**
- The **"Whole hold" entity is marked by the keep glyph** in household-amber — members carry identity-color dots; the hold carries the brand object.
- **A hold has a timezone.** `families.timezone` (IANA, NOT NULL). The canonical clock for every household-local date — see *The household clock*.

## Registers and modules
**Registers are aesthetic. Modules are functional. They are not the same axis and they never gate each other.**

**The four registers — Forge · Garden · Keep · Hall** — are an avatar class and an ambient theme. A hearthmate picks one because they like it. **A member's register has no relationship to what their hold has enabled.**

> **SUPERSEDED (2026-07-25, same day):** the reading that the four registers were four functional layers. It failed decomposition. **Keep** (upkeep, chores, repair, DIY) is quests. **Garden** (yard, planting, outdoor maintenance) is quests. **Hall** (celebration, hosting, parties, feasting) is lists, campaigns and calendar events. All three are content and theme on machinery that already exists — which is what a register *is*. Only fitness failed to decompose into a quest, because it is the one household activity that **cannot mint embers without breaking constitutional rule 2**: a kid's ember requires adult verification, and nobody can verify a squat set. **Fitness is the odd one out, and it is the only module.**

**Module enablement is household-level, offered at the END of onboarding, and reversible by an adult from a Hold profile screen** *(LOCKED 2026-07-25)*. Stored as `families.enabled_modules text[]`, written **only** through the `set_enabled_modules()` RPC.
- **It is a binary and it is a BRANCH, not a toggle.** There is one module, so there is one question: Emberhold, or Emberhold with Forge. Selecting Forge does not set a flag and continue — it **launches a second setup flow** (equipment, experience, goals). That is precisely why it belongs at the end of onboarding rather than buried in settings: a settings toggle that fires a multi-screen wizard is the worst of both.
- **Sequence: hold setup → Forge? → (Forge setup if yes) → first-quest doorway → board.** The activation doorway stays **last**, so momentum carries straight into a populated board. A five-screen detour wedged between "post your first quest" and "see it on the board" breaks the one handoff that cannot afford it.
- **The RPC is load-bearing and not a style choice.** `families.is_founder` is service-role-write-only, and that is what makes the paywall enforceable. Granting `authenticated` table-level UPDATE on `families` so a screen could write a feature column would have re-opened the paywall hole. The RPC means **zero new grants on `families`** — `authenticated` still holds column-level UPDATE on `name` only.
- **Never per-member** (constitutional rule 4).
- **Never offer a module that doesn't exist.**
- **Reversible.** Disabling hides surfaces; it never destroys data.
- **The column is correctly named as shipped and needs no migration.** `['household']` = base; `['household','fitness']` = base + Forge. A `text[]` for a current binary is mild over-engineering that costs nothing and stays honest if a second module ever appears. **The only change owed is the allowed-set string in `set_enabled_modules()` — `'training'` → `'fitness'`, free right now because nothing has ever written that value.**

---

## Architecture (the bones)
- **Everything is an "entry."** Shapes: quest, calendar event, list, list item, meal. Only **quests** carry the platform game.
- **The backbones:** the **calendar** (one timeline, constitutional) and the **ember economy** (every platform feature is a source, sink, or pool).
- **Three horizons:** Engine (daily quests), Campaigns (seasonal collective goals), Archive (the forever adventure log).
- **One spine for movement:** the **activity feed** (`activity_log`) — an append-only stream every meaningful state change writes to. The engine under notifications, wall callouts, and the Adventure Log.
- **The `+` is the universal capture.** Inside a create flow it transforms into that form's submit action. On a single-purpose surface that owns its own create action (a list's docked add-item bar), the `+` FAB is suppressed entirely.
- **Personal vs shared views are filters, not separate data.** *(Proven twice — the quest audience filter and the reward audience rail are both WHERE clauses, not second screens.)*
- **Role-aware home.** Adults land on **the Briefing** (operations), Kids land on the **game board** (play). Same nav slot, different surface.
- **THE MEMBRANE.** The platform game lives in **quests only**. Calendar, Lists, meals and notes are clean utilities that may *optionally* spawn a quest, and have no game at all. Gamify the chore, not the grocery item. Lists writes nothing to the activity_log spine.
  - **Module amendment** *(LOCKED 2026-07-25)*: **a module may carry at most ONE game of its own, sealed from the platform's.** The membrane exists to stop *every* surface becoming a game; "at most one per module, sealed" preserves that while allowing an optional module its own. **No module currently exercises this** — the Forge ships as a tool first.

## The audience pattern (a first-class architectural primitive)
**A nullable/defaulted `audience` enum on an object + a viewer-role WHERE clause on its read query.** No second screen, no duplicated component, no parallel table.

- **`quest.audience`** — `anyone` / `adults_only` / `kids_only` (default `anyone`). Scopes an *unassigned/open* quest. The form control shows only for unassigned quests and hides the moment a quest is directed.
- **`reward.audience`** — `household` / `adults_only` (default `household`). Powers the couples rail.
- **The rule:** membrane-safe. Audience changes **visibility only** — never the sink, never the flow, never eligibility to earn.

Any future "X should only be seen by Y" want gets answered with this pattern before anyone proposes a new surface.

---

## The household clock (LOCKED 2026-07-23)
**The canonical clock for any household-local date is HOUSEHOLD-local, derived server-side from a single named function.** Not client-local stamped at write time, and never server UTC.

- **The mechanism:** `families.timezone` (IANA, NOT NULL) + **`household_today(fam uuid)`** (STABLE). Every expression touching `due_date` derives from it.
- **`CURRENT_DATE` is banned in any expression touching `due_date`.** It is UTC, the hold is not, and the gap is a whole timezone's worth of wrong for several hours a day.
- **Why not client-stamped:** `handle_quest_approval()` is a BEFORE-UPDATE trigger. It has no caller and no place to accept a client-supplied date, which structurally kills the stamp-at-write remedy. That the client-supplied-date trust-widening evaporated with it is a bonus, not the reason.
- **This is a class, not an instance.** Reported at one site; enumeration of `supabase/` found **six** live violations across two functions.
- **Open, Gate B:** the column defaults to `America/Los_Angeles` — correct for every current hold and **silently wrong for the first stranger in Ohio.** Capture `Intl.DateTimeFormat().resolvedOptions().timeZone` at hold setup.

## The activeness model (LOCKED 2026-07-21)
**`isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts` is the single shared predicate.** Every surface that asks "is this quest live right now" imports it. None defines its own.

- **This exists because three surfaces each defined "active" independently** and drifted apart — a defect *pattern*, not a bug. Board, Briefing and wall are unified behind it.
- **Any new activeness surface imports the predicate or is wrong by construction.**
- *A fourth surface (the member profile / roster active-quest list) is under investigation — see `status.md`.*

---

## Data model — the Quest object
- `title` — short text
- `points` — integer *(internal field name; user-facing term is **embers**)*. **Minimum 1 — zero-ember quests are illegal.**
- `created_by` — member who posted it
- `assigned_to` — empty = open (anyone claims); set = directed
- `audience` — **anyone / adults_only / kids_only** (default anyone). Model, form, **and consumer all live**: kids see `anyone` + `kids_only`; adults see all three.
- `campaign` — empty = everyday; set = tagged, its embers feed that campaign's bar
- `status` — open → claimed → submitted → approved (directed quests start at claimed)
- `approved_by` — the actor holding approval authority; **embers only count once set**
- `due_date` — `DATE NOT NULL`. **Backend-only — never user-facing.** Recurrence machinery, not user information. The board and every open-bounty surface filter `due_date <= household_today()`; recurrence spawns the next instance *dated forward*. **This gate is load-bearing** — it keeps the board a "what needs doing now" surface rather than a dump of the table. *(The column still carries `DEFAULT CURRENT_DATE` deliberately, recorded so it isn't rediscovered as an oversight; nothing relies on the default.)*
- `recurrence` — none / daily / weekly / **monthly**
- `rating` — optional 1–5, quality signal only, does NOT change embers
- `is_favorite` — star → appears in a quick-add template list
- `tier` — derived from points: **DIM → WARM → HOT → BLAZING**
- Timestamps: created, claimed, submitted, approved

### The recurring lifecycle (LOCKED)
- **Fixed calendar anchors.** Weekly → the **Monday** of the following week; monthly → the **1st** of the following month; daily → tomorrow. There is **no day-of-month selector** — it was removed. Monthly is always the 1st; anything bespoke is a **calendar event**, not a recurring quest (the membrane). Anchoring is by construction (`date_trunc('week'|'month', …) + interval`), which kills the relative-drift where a late approval walked the due date forward.
- **Spawn on approval only. There is no timer.** On approval of a recurring quest, archive the completed instance and spawn the next — **never reset in place**. One shared spawn path: `handle_quest_approval`.
- **The successor anchors to the household's today, not to the completed instance's old date.** Anchoring to the archived instance walks the whole series backward and was a real shipped bug.
- **Per-cadence rollover is asymmetric, deliberately.** Daily **rolls forward on miss** — `roll_missed_dailies` archives the stale instance and spawns a fresh one for today: no ember accumulation, no guilt-pile. Weekly/monthly do **not** roll — an undone weekly/monthly persists at its original `due_date` until approved, because approval is the only spawn trigger. Consequence: an undone monthly never auto-refreshes and never blocks its own next cycle; it lingers, visibly, which is correct — it's still owed.
- **`roll_missed_dailies` fires on every board mount, unconditionally.** Any create flow that navigates to the board runs it seconds later, so a freshly created daily is judged by it immediately. This is why its comparison must use the household clock: judged against UTC it declared same-day rows "missed," archived them, and respawned them dated tomorrow — correct for one round-trip, then clobbered.
- **Rollover has no health indicator.** A stuck daily and a healthy one are indistinguishable from the glass. Logged as an observability gap.

**Adult self-completion:** when the submitter is an Adult, completion writes `status='approved'` + `approved_by=self` **atomically** — embers mint immediately, never entering the pending queue. **The gate is *satisfied*, not bypassed** (constitutional rule 2). Kid completions unchanged.

**Two load-bearing triggers — do not drop or refactor without replacing the guarantee:**
- **`a_enforce_quest_update_authority`** — BEFORE-UPDATE, fires before the approval handler, restricts approval to adults. **The lock on the ember economy and the enforcement point for constitutional rule 2.**
- **`enforce_quest_family_refs`** — rejects any quest whose `claimed_by` / `assigned_to` / `approved_by` points outside the quest's own family. The cross-tenant guard.

**Planned extension (parked):** `objectives[]` — an array of checkable steps turning a quest into a **multi-step quest**. Steps don't mint; only turn-in does, and submit is locked until every step is checked.

---

## Onboarding (Pip-guided)
The ember-sprite mascot, **Pip** (soft-named; kids retain veto), is the app's guide. Pip is **guidance only** — read-only, never awards embers, unlocks, or gates anything.

**First-run is contextual setup, not a feature tour.** The sequence, in order, and the order is load-bearing:

1. **Hold setup** — signup, then a Pip-guided screen building the rest of the hold (hearthmate repeater: name / avatar / role; Adult requires PIN). Signup itself creates the account holder only.
2. **Timezone capture** — silent, from the browser. See *The household clock*.
3. **The Forge question** — one binary at the **end** of hold setup. Emberhold, or Emberhold with Forge.
4. **Forge setup — only if selected.** A second flow (equipment, experience, goals). Skipped entirely otherwise.
5. **The first-quest doorway — always last.** **A new hold currently lands on an empty board with no path to a first quest.** Onboarding ends at setup, not at activation, and that is the top structural gap in the product.

- **Joiners** skip hold-building. **Kid first-run:** welcome card + 3 skippable coach-marks. **Persistent help:** Pip in the top header corner → role-aware help sheet + "Replay the intro" / "Add more hearthmates."
- **Feature tour is on-demand** — offered only once the hold has content. The loop card (**"Post the work → Claim & complete → Approve & reward → Chase the campaign"**) doubles as the marketing pitch and is the intended centerpiece of the landing page.

## The economy
- **Sources:** quests mint embers, on approval by an adult (adults self-approve atomically). **Sinks:** the **Vault**. **Pools:** campaign collective bars.
- **Earned vs. spendable split.** Ranks tracks **lifetime embers earned** and is never reduced by spending. The Vault draws from a separate **spendable balance** (earned − redeemed). **Spending never costs rank.**
- **Two reward rails:** the *free* rail (spectacle, status, the win) and the *costed* rail (the Vault). For young kids the free rail may carry most of the daily motivation — delight cooperation rather than buy it.
- **Individual redemption is the model.** Rewards are redeemed by a person, from their own balance. Pooled/shared earning is **not** a Vault feature — it is what Campaigns' EARNING type is for. *(This is why Date Night is a Campaign, not a reward.)*

### The reward tiers
Rewards sit on the same **DIM / WARM / HOT / BLAZING** ramp as quests. One ramp, both directions of the economy.

### The adult reward menu (v1 — LOCKED, 7 items)
Adults earn and spend too — this is what makes the app a household tool rather than a kid tool with adult admins.

| Tier | Reward |
|---|---|
| **Dim** | Coffee / breakfast delivered to you |
| **Dim** | Pick dinner |
| **Warm** | Skip one chore-quest |
| **Warm** | Pick movie / show night |
| **Hot** | One guaranteed hour alone |
| **Blazing** | Solo morning routine (someone else runs the morning) |
| **Blazing** | Solo bedtime (someone else runs bedtime) |

**Deliberately excluded — dishes.** It already exists as a quest. The same chore cannot be both a source and a sink.

**Deliberately relocated — date night.** Moved *out* of the Vault and *into* Campaigns as the EARNING type's first real content.

---

## Platform surfaces

### The Engine
Quest board, ember economy, recurrence, ratings, favorites, adult approval, Ranks leaderboard, Quest Log.
- Board is **date-windowed** (`due_date <= household_today()`) and reads activeness through the shared predicate.
- **Scope badges (adult view only):** `adults_only` → "Adults only"; `kids_only` → "Kids only"; `anyone` → no badge. Not shown on the kid board.
- **Claim-eligibility copy states itself exactly once per card.** `anyone` keeps the "Available to anyone" subline and gets no badge; scoped quests show their badge and **drop** the subline.
- **The Quest Log applies no `due_date` filter.** Currently load-bearing as a diagnostic surface — it is how both date bugs were confirmed. Whether that stays is an open deliberate call.

### The Vault (the reward store — dual-mode, both roles)
The store **switches mode on a binary affordability trigger.** Affordability-as-heat.

**Kid view:**
- **ZERO affordable → CATALOG ("The Wish Menu").** The full ladder by tier, cheapest-first, everything visible, everything favoritable. Header: "Nothing in reach yet — pick what you're grinding toward."
- **ONE+ affordable → CURATED.** In-reach glowing set (up to 2 at/under balance) + live **Redeem** + "N embers left after" + next-unlock taunt(s) with progress bars + per-tier fold.
- **Favorites are additive, never an override.** A goal pinned in catalog mode carries into curated mode.
- **Popularity / highest-redeemed is explicitly rejected as a curation signal** — it structurally buries the aspirational rewards, which are the ones doing the motivational work.

**Adult view — three zones:**
1. **Awaiting Your Approval** — pending *kid* redemption requests. Each card shows **redeemer name + face** (the kid-impersonation mitigation), reward, cost, timestamp. Empty state: "The hearth is quiet — no requests waiting."
2. **Your Store** — the adult's own balance-anchored store, **reusing the kid store component**. Adults redeem instantly.
3. **Reward Menu · Manage** — edit/delete/add rewards. No redeem buttons, no affordability text.

**Reporting deliberately does NOT live here.** Redemption history and reward-performance belong on a future admin surface — Emberhold's first *analyze-vs-operate* split.

**The couples rail (`reward.audience`).** `household` / `adults_only`, default `household`. Membrane-safe: only visibility changes.

### Campaigns
Shared-goal containers a quest tags into. Two types via `goal_type`: **PLANNING** (completed tagged quests / total) and **EARNING** (pooled embers — **the home for shared/couple goals**). Planning tools, not earning grinds. Progress renders as a bare **%**. Completion fires the **Legendary** override state on the feed.

### Calendar
Standalone event CRUD: month grid, per-member color dots, today-glow, day-tap list, event detail. Events carry who / date / optional `end_date` / times or all-day / location / notes / simple recurrence. The keep glyph marks whole-hold items. **Native OS date picker.** Event **creation** is a feed write point; edits aren't. No iCal import yet. **One timeline is constitutional — every module's dated things land here, including workouts.**

### Lists
A generic shared-list primitive (`list` / `list_item` / `list_section`). Index + detail (docked add-item bar, **strike-and-sink**, user-defined **sections**, per-item delete, "Clear checked"). Seed lists: Groceries / Packing / Errands. **Deleting a whole list is adult-only.** **Off the activity_log spine** (membrane).
- **Add-item bar = full-width solid docked footer.** The global FAB is suppressed on list-detail.
- **Search bar** appears past ~50 items; **searches checked items too**; retains section grouping.

### The Briefing
The Adult command center and the **Hub's seed**. Zones, priority-ordered by brightness=heat=importance: **NEEDS YOU** · **OPEN BOUNTIES** · **FEED** (48h window) · **THE ROSTER** (2×2 portraits, heat-dial) · **ON THE HORIZON** (rolling 3-day) · **CAMPAIGNS**. Plus the Quest Log link.

### In-app notifications
Global header bell. Badge = actionable items for the current profile only. Panel: **NEEDS YOU** + **RECENT** (~10 latest activity_log items). **Stateless** — no read/unread, no new table.

### The wall (display / kiosk mode)
Always-on, no-login, full-screen on a cheap tablet. Top full-width FEED ticker; bottom-left ~3/4 calendar WEEK view; bottom-right ~1/4 avatar-collapsible ACTION RAIL. Rotating ambient cards, 5–10s.
- **THE WALL SHOWS AND PROPOSES. IT NEVER MINTS, SPENDS, APPROVES, OR EDITS.** Claim/complete move status only; approval stays PIN-gated. On-behalf writes (`wall_request_redemption`) *request*; they never commit.
- **The only semi-public Emberhold surface a non-customer can encounter** — which is why wall-visibility ranks cosmetic catalog items.
- *(The garage tablet is a different job: an **input** device at arm's length mid-rest, not a display glanced at from across the room.)*

---

## Navigation
- Bottom tab bar, **7 tabs**: BOARD · CAMPS · CAL · **LISTS** · VAULT · RANKS · YOU.
- The **"+" FAB is raised** to a kiss-overlap above the bar. It opens the create menu; becomes the docked submit inside a create flow; is suppressed on single-purpose surfaces.
- **Open question:** seven tabs is already one past mobile comfort, and an enabled Forge needs somewhere to live. **It is not "add another tab."**

---

## Avatars (the hero roster)
- **Hero portraits are the sole avatar system.** Never a bare photo in the circle — a raw photo breaks the style floor and would make every hand-crafted portrait read as set dressing. *(Long-term the "I want it to be me" want is served by a photo→stylized-hero render pipeline that pushes an uploaded face* into *the floor.)*
- The picker is grouped by the four registers and **collapses per register** to ~4 portraits with "show more / show less."

### The four registers
**Aesthetic only — a register is not a functional area and does not gate anything.**
- **FORGE** — warriors, protectors, ember-forged heroes
- **GARDEN** — nature-touched growers and wildsfolk
- **KEEP** — steadfast watchers and guardians
- **HALL** — the whole gathering hall: makers **and** entertainers **and** keepers of warmth (bards, storytellers, dancers, revelers, hearth-tenders, hosts). **Food is ≤ ~1/3 of the register.**
  - *Renamed from **FEAST** (7/03) — a redefinition, not a label swap. "Feast" narrows to food (a generator fed the word returned 6/8 food characters). The constraint is permanent: if anything named Hall ever reads as a food surface, it has re-narrowed to Feast and it is wrong.*

### The roster
**48 characters, 12 per register.**
- **Register spread (LOCKED):** every register must span **cute / majestic / COOL**. **Every register carries 2–3 genuine kid-magnets** *and* a cool/battle-ready character. Generators underweight the cute end by default and regress to one token each unless explicitly pushed. Validated by which avatars the kids actually reacted to — which is the entire product thesis.

### The Avatar Style Spec (LOCKED — the band)
Coherence is governed by a **wide style band with a hard floor**. **The floor is a floor, not a ceiling** — different registers cohere because they share the floor, not because they resemble each other.

**The floor — three load-bearing constraints (all must hold):**
1. **LUMINOSITY** *(most important)* — the subject glows from within / carries strong warm ember rim-lighting; light comes OFF the character. Never flat, dull, or muddy.
2. **STYLIZED ILLUSTRATION** — clearly hand-drawn painterly fantasy art. NOT photoreal, NOT gritty oil-painting realism, NOT flat chibi/mascot.
3. **SATURATED JEWEL COLOR** — rich, clean, saturated color. Never muddy brown earthy naturalism.

**Composition rule (LOCKED): circular-crop-safe framing.** Centered with even margin; full head (crown / horns / hat included) + shoulders inside a centered circle with breathing room, nothing touching edges.

**Background rule:** each background derives from THAT character, kept dark enough to blend into `#1A110B` in a circular crop — a FILLED portrait, not a transparent cutout. **No uniform gold frame.** The rim-light unifies the roster; a frame does not. *(Watch the leak: the luminosity constraint tends to bleed into the background and set everything on fire.)*

**Diversity rule:** humanoids default to one young light-skinned face unless explicitly fenced. Name the archetype traps — silver-locs elder, curly-haired beauty, big-eyed elf child, generic young-hero man — as **"at most once in the entire roster."**

**Asset hygiene:** image gen always lies about "transparent background." Sidestep with filled-dark backgrounds. For any cutout need, generate on flat pure-green and chroma-key; verify alpha programmatically.

### How to actually generate avatars
**The method lives in `playbooks/avatar-generation.md`.** Read it before touching a generator. Headline: the style block controls output by **axis, not length**; generate the **full roster in one run** so the anti-repeat constraint can bind; prompt the cool register through **material and light**, never supernatural vocabulary.

---

## Monetization (the Founding Guildhall)
- **One v1 SKU: the "Founding Guildhall."** A **one-time, household-level** purchase. Stripe Checkout + Supabase webhook + `families.is_founder` (**service-role-write-only** — an owner cannot self-grant it). Staying outside app-store rails retains **~97%** of revenue.
- **What it unlocks:** the paid share of the avatar roster. Free keeps **16** (four per register); the Guildhall opens the remaining **32**. The gate is a DB value (`system_flags.founder_gate_enabled`) — flipping it is an UPDATE, not a build.
- **The free tier must be genuinely COMPLETE, not a demo.** Every free four needs a genuine kid-magnet.
- **Household-level, always** (constitutional rule 4).
- **Membrane-safe cosmetics.** A paid avatar is not a better avatar.
- **The catalog is retention leverage, not acquisition insurance.** **Wall-visibility ranks catalog items.**
- **Next catalog item:** living-hold ambient themes, one per register, canvas particle-based. Keep first, as the performance proving-ground.
- **A sharper test than "delight vs. function": is this thing a CHOICE or a MEMORY?** Expression is choice — always free, always reversible, outcome-neutral. A **mark** is memory — earned through something that actually happened, and what is paid for is the ritual of permanence. Themes are choice. An artifact produced by six months of real effort is a memory.

---

## Design system
- **Ember-lit aesthetic.** Warm charred darks, never cold gray. Base `#1A110B`, elevated card `#241813`, top-edge sheen `#6E4A2E`, primary text `#F2E3CB`, muted `#A07B54`. Ember ramp (deep→bright): `#BA7517` → `#EF9F27` → `#F8C13C` → `#FCDE5A`; gold rim `#E0A94A`.
- **Brightness = heat = importance** (constitutional rule 6).
- **Member identity colors are a SEPARATE system from the ember ramp.** Amber/gold `#E0A94A` (also "Whole hold" default), violet `#9B6BD6`, jade `#3FB37A`, steel-blue `#4A9FD6` (spare: rose `#E0607A`, teal `#2DB3A6`, coral `#E08750`).
- **The household keep glyph.** Tintable SVG, household-amber, ~20–22px inline.
- **Date pickers.** Calendar event dates use the **native OS picker**. Quests carry no user-facing date input.
- **Numeric inputs must be clearable.** A number field that refuses an empty intermediate state is unusable on mobile.
- **Depth, not flat:** layered surfaces, a 1px warm top-edge highlight, soft shadow beneath cards, glow on heat.
- **The FAB-as-submit / FAB-suppression pattern.** **Exactly one create control in the bottom thumb zone, always.**
- **Layered docked surfaces:** a docked input over a scrolling list must be a full-width **opaque** elevated layer with a hard top edge and a short fade scrim — content scrolls *behind* it, never *through* it. Translucent floating capsules over scrolling content are banned.
- **Brand: two marks, one keep.** App icon (round-towered keep + glowing hearth on a charred tile) = universal small mark; login crest (the same keep in a forged-gold heraldic shield) = ceremonial mark. Motto **"by hearth & hold."** Plus **Pip**.
- **A register's palette may never override the ember-lit base or the brightness rule.** Registers are a skin on one house.

## Experience layer (the juice)
- Completing a quest is theater (Battle Chess principle). **Tier = intensity, register = theme — no new data model.**
- **Reactive micro-motion:** ember burst by tier, approval echo, new-approval pulse, count roll-ups, press feedback, breathing submit button, plus a subtle state-driven household-warmth shift.
- **AI-generated, never stale:** grow a library of themed vignette assets in the background, cache, select instantly at the win. Bake an alpha-check into the pipeline.

## The activity feed (the event-log spine)
A single **append-only, permanent, immutable** stream.
- **`activity_log` table.** Fields: `id`, `actor_id` (soft, nullable), `actor_label` (frozen), `verb` (enum), `object_id` (soft, no hard FK), `object_label` (frozen at write), `ember_delta` (nullable), `campaign_id` + `campaign_label` (nullable), `significance` (**Legendary** override only), `created_at` (indexed).
- **`logActivity()` helper** — the single app-level write path. No DB triggers.
- **Five curated write points:** `quest_approved` · `bounty_posted` · `reward_redeemed` · `campaign_completed` · `event_created` (ember_delta null — membrane).
- **CAP THE READ, NEVER THE WRITE.** The table is permanent. **No pruning queue, ever.**
- **The actor must be derived server-side.** `actor_label` is currently a client-supplied parameter, making the receipt surface forgeable within a hold — the one place where "a kid could do a thing" becomes "a kid could make the record say an adult did it." Derive from `auth.uid()`.
- **The feed is where an optional module touches the platform.** A module may write to the spine — that is presence, not economy. Same theater, no exchange rate.
- **Downstream consumers:** the notification RECENT panel · the full in-app feed view · wall/kiosk callouts · the **weekly recap** · the Adventure Log · the family message board.

---

## Security posture (design truth, not just a bug list)
- **The core rule: embers mint only on approval by an adult** — constitutional rule 2, enforced at the trigger layer.
- **The tenant boundary holds under authenticated cross-tenant attack (VERIFIED 2026-07-19).** All cross-tenant reads/writes, `current_family_id()` integrity, every household-scoped SECURITY DEFINER RPC, the `family_xp` view, the insert trigger, and `system_flags` write are SECURE.
- **Load-bearing grant rules (LOCKED — do not regress):**
  1. **`member_spendable()` is family-scoped.**
  2. **Quest FK columns resolve to the quest's family** — `enforce_quest_family_refs`.
  3. **`families.is_founder` is service-role-write-only.** This is what makes the Guildhall entitlement enforceable.
  4. **`founder_gate_enabled()` / `my_household_is_founder()` execute is `authenticated`/`service_role` only.**
  5. **Module enablement writes only through its RPC** — no table-level UPDATE grant on `families` is ever added to support a feature column. Adding one would re-open rule 3.
- **GRANT DRIFT IS A CLASS-LEVEL DEFECT, NOT AN INCIDENT.** Postgres grants `EXECUTE` to `PUBLIC` by default on every newly created function, and table grants have diverged in **both** directions — once too narrow (14 tables lost `authenticated` Data-API grants) and once too wide (`anon` holding full CRUD on `families`, including UPDATE on `is_founder`). **Revocation must be proactive.** Convention: grant `authenticated` first, then revoke `PUBLIC` — order matters.
- **Hand-applied DB changes are forbidden.** Every change lands as a migration file. Structural changes verify against the **live** surface (`information_schema`), not migration history.
- **Schema is undone FORWARD** *(LOCKED 2026-07-25)*. A Lovable version-revert restores code and project files **only**: applied migrations are not rolled back, objects stay live, and the migration *files* are removed while their objects remain. There is no mechanism to roll back a specific migration; undoing schema means writing a new migration that reverses it. Drop in FK order **without CASCADE**. Run a read-only live-schema recon before any teardown.
- **Join codes admit members to a household — gated by admit-on-approval.** A join-by-code creates a `profiles.status = 'pending'` profile; **`current_family_id()` returns NULL for any non-active profile**, so every family-scoped RLS policy denies by construction. The self-selected role is stored as `requested_role` (advisory only); **no `user_roles` row is written at join.** A parent admits *and* confirms the role in one action (`admit_pending_member`). **Do not refactor `current_family_id()` without preserving the NULL-for-pending guarantee.**
- **The walk-up trust boundary (accepted, intra-household).** In the shared-session model a device-kid holds the owner's ambient parent JWT; the PIN is the only kid/parent line and it is client-side. **Intra-household, not cross-tenant.** Two scanner findings are ignored *because of* this boundary; deciding the own-session-vs-per-member-auth fork revives both.
- **Kid-vs-kid impersonation.** The client-side profile switch is ungated. Kid PINs stay **off by default** (they tax the walk-up thesis). Mitigation already built: the **redeemer's name + face on the adult approval card**.
- **Persistence debt:** Vault favorites use `localStorage`. Fine for validation, fatal for the shared-wall model.

---
---

# PART II — THE FORGE (the fitness module) — SHAPE UNDECIDED

**Status: not built, and its shape is an OPEN DECISION.** This section records what is settled and names the fork. It is deliberately not written as design truth, because it isn't yet.

**Names:** stored value `fitness`; product name on screen **Forge**.

## What is settled

**It is a module inside Emberhold, not a sibling app** *(LOCKED 2026-07-25)*. A separate product, tenancy, and domain is DECLINED. The standalone frame reached a domain recommendation, an eight-table schema, and a working slice before it broke — on **proportion**: it had grown a tool wall, a metal-composition system, and project-scoped blocks while still being unable to log a working set. Merging fixes the ratio and deletes work: no second auth, no second tenant bootstrap, one member table, one PIN system, one avatar pipeline, one Founding Guildhall unlock.

**Why it is the only module.** Every other candidate area decomposes into quests, lists, or campaigns. Fitness does not, because it **cannot mint embers without breaking constitutional rule 2** — a kid's ember requires adult verification and nobody can verify a squat set. Either the approval gate becomes a rubber stamp, corrupting what approval means everywhere including chores, or fitness effort does not mint embers. **It does not.**

**The engine is pure client-side TypeScript, and progression logic never lives in Postgres** *(LOCKED 2026-07-25)*. `src/lib/progression.ts` exists today: RPE-driven autoregulation, plate-snapping against an equipment inventory, Epley+RIR e1RM, 10/10 tests, zero Supabase imports.
- **It lives in the client for one non-negotiable reason: a garage has unreliable connectivity.** If the rule lives in the database the app is useless the moment wifi drops. Supabase is the sync target, never the compute.
- **Tests are mandatory here specifically** — a silent off-by-one corrupts training data that cannot be reconstructed.
- It survived a full eight-table teardown intact, which is what having no Supabase imports buys.

**The differentiator is the reason string, not the math.** Loggers are commoditized with generous free tiers; generators are widely criticized for making decisions users must override without explaining why. *"3×5 @ 185, avg RPE 6.7 — all sets complete under target, adding 10 lb"* beats an unexplainable algorithm and costs nothing to build.

**The v1 job is to replace Fitbod.** Set logging, automatic programming, rest timing, progressive adjustment, and the reason string. At $15.99/mo, that is ~$192/year against a $636/year break-even target — roughly a third of break-even recovered before a stranger pays anything, with no paywall, funnel, or Gate E dependency.

**Free/paid split** (constitutional rule 7): deterministic autoregulation, template-driven auto-programming, rest timing and progressive adjustment are **FREE**. Only *LLM-generated* programming is paid — the sole feature with a real per-user marginal cost.

**Participation is not a role check.** "Adults only" is a proxy for "trains" and will break. When it does, the answer is a per-member flag.

**Explicitly out of scope:** client-facing training (paying clients, not hearthmates). A client is not a hearthmate and cannot share a household tenant. If it ever happens it is a separate trainer-facing surface reading the same data.

**Platform constraint, and it is hard.** Heart-rate-validated work requires Apple Health / Google Fit. **There is no HealthKit web API**, and the PWA posture is LOCKED (Capacitor assessed and DECLINED 2026-07-22). **Self-logged strength work is the only modality a PWA can honestly measure.** Distance, interval and mobility work validated by heart rate are structurally unavailable until and unless the platform posture changes.

## The open fork

**Option A — TOOL ONLY.** A clean utility with no game, no currency, no collection. It writes presence to the activity feed and nothing else. Recommended as v1: buildable now, cancels a subscription this month, and tests whether the module is used before anything is built on top of it.

**Option B — TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as a collection that is legible evidence of what was actually trained. No currency in the fungible sense — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.

**What Option B would owe before it could be written as truth:**
- Whether materials pool or are claimed at commitment.
- What materials are typed **on**. Typing by modality contradicts a prior locked call elsewhere that all modalities produce equal value — and with one available modality there is nothing to type across anyway.
- Whether a collection survives the calibration check: *this is a family chore game with your kids' faces in it.*

**The standing risk, named because it has already happened once.** The game is the most interesting part to build and the tool is the part that pays. **Option B does not get designed until Option A has been used for a month.**

---

## Tech & working model
- **Stack:** Lovable.dev (React + Supabase), mobile-first installable PWA. **Stripe Checkout + Supabase webhook** for the Guildhall. **Copilot Create** for avatar/asset generation (work account — zero Lovable credit burn). **Claude Code** for the codebase-reading jobs Lovable can't do from inside itself.
- **The lanes (LOCKED):**
  - **Scott** — vision, taste, final decisions. **Anything whose success criterion is visual.**
  - **jAIne** — hears, challenges, breaks down, translates intent into direction. **Cannot see. Does not brief visual work as if she could.** *Does not reach into Lovable's lane.*
  - **Lovable** — engineers the functional output. Default lane for frontend and live-DB work.
  - **Claude Code** — text: code, config, strings, files, structure. **Syncs to `origin/main` before reading.**
  - **The edge of Code's lane:** the moment the success criterion is *"does this look right,"* Code is outside it — and briefing harder does not help, because the briefer is blind too.
- **One writer at a time.** Data-layer / live-DB → Lovable. Frontend text/code → Claude Code. Never simultaneous.
- **Brief the floor and the intent; leave latitude on execution.** **jAIne's failure mode is over-specifying when under-trusting downstream.** *(Exception: high-stakes live-DB audits get a directive, precise roadway.)*
- **Fetch the canon before producing anything.** Not ceremony — it is what catches an invented noun in thirty seconds instead of eight tables later.
- **Recon before build. Brief recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X.
- **Sweep the class before fixing the instance.** Seven functions carrying a grant default; six date sites across two functions; one sampled table with wide-open grants implying twenty unread ones.
- **Decompose before you promote.** A thing that looks like new architecture may be existing machinery wearing a name. Three of four proposed functional areas dissolved into quests and lists under inspection, in the same session they were proposed. **Run the decomposition before writing the doc.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.** Read what landed against what was briefed, every time.
- **A defensive layer is not verified by the migration that adds it.** Fix → re-read the catalog → confirm on the live artifact. **HTTP 200 is not "renders."**
- **"Shipped" means a completed end-to-end loop, verified on the glass.** **Design-complete ≠ shipped.**
- **A Code job isn't done until the artifact is observable from outside the agent.** Verify `origin/main`, not the agent's report.
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (tenant-isolation audit and P4×L8-class work).**
- **The calibration check:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that is information.

## The other docs
`north-star.md` (why + the gate ladder — read first, cold) · **this spec** (what it is) · `status.md` (where the build is) · `parking-lot.md` (what might be) · `decisions.md` (what we decided and why) · `playbooks/` (how to do a thing) · `archive/` (reasoning history). Full manifest: `canonical-manifest.md`.

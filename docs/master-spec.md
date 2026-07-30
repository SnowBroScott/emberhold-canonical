# Master Spec
**What Emberhold IS.** Canonical design truth — above any individual build prompt or chat. Status lives in `status.md`; this doc does not track it.

Last substantive update: **2026-07-29** — the seven-fold pass, plus the completion marker shipping the same day. Folded, all overdue: the creator onboarding flow and its shell (07-26), signup posture (07-26), the write-once timezone and its heal (07-26/27), Part II's rescoping to household-only (07-27), the non-creator first run and the completion-marker seam (07-27), Part II's v1 shape (07-28), and the onboarding-sequence change and PWA install door (07-29). Corrected: the `actor_label` derivation prescription (a killed regression), the dead "fourth activeness surface" note, the false `set_enabled_modules` claim, the COPPA shield, grant drift's severity, Part II's client-engine rationale, and the "empty board = top structural gap" line — the first-quest doorway is BUILT. Then amended in the same session: the marker shipped and was walked end to end, the RLS question was answered (and did not matter), the kid-marker read/write cancellation was found, the arrival gate's exemption gap was shipped live and fixed, and the redemption-approval deadline was **breached**.

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
- **The mascot is PIP. LOCKED 2026-07-26** — no longer soft-named, no kid veto outstanding. Cinder and Holt are DECLINED: both fine names attached to a character that already has one. Pip's name is live on the auth screen and across every first-run screen; the decision was effectively made by deployment and is now written down so nobody starts a rename.
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
- **The rule pays a dividend nobody designed for.** Forge's display mode uses the existing roster component as its session lane — one avatar per concurrent lifter. Reading people from the member table wasn't just hygiene; it handed the module its layout.

**6. BRIGHTNESS = HEAT = IMPORTANCE.** The hierarchy principle, the animation-intensity dial, the avatar-luminosity floor, and the Vault's affordability-as-heat mode switch are one rule wearing four hats. Every module and register inherits it.

**7. FREE IS A FULL TOOL. PAID IS DELIGHT, *OR* MARGINAL COST.** *(Amended LOCKED 2026-07-25.)*
The membrane applied to money. Paid may never be access to basic function — **with one generalized carve-out**: a feature with a genuine, unavoidable **per-user marginal cost** may be paid.
- **GUARDRAIL, so the clause doesn't rot:** the cost must be *real and unavoidable, not architected into existence.* Anything can be made to "incur cost" by routing it through an API on purpose. If a feature could run on-device or as a deterministic rule and a model call was added to justify a paywall, that is the rule being gamed against itself. **Paywalling a rest timer would be indefensible.**
- **The named test case is SUSPENDED, not deleted.** *LLM-generated* programming was the clause's live example, with deterministic autoregulation, template programming, rest timing and progressive adjustment free. **Forge is scoped to one household (2026-07-27), so nothing in it is currently paywalled and the split does not bind.** The rule stands as written and the test case reactivates the day Forge meets strangers.
- Generalizing rather than granting an exception is deliberate: an exception invites the next case to be argued from scratch; a clause gives a test for video hosting, exports, and anything else that bills per user.

---

## The hold (the tenant)
- **Members ("hearthmates"):** name, avatar (hero portrait), role (**Adult / Kid** — display label; role enum unchanged), class (**Forge / Garden / Keep / Hall** — the register).
- **Model:** profile-switching, shared-device first (Netflix-style — tap to switch), because young kids have no phone. Join-code is the *secondary* path.
- **Kids have no auth identity ON THE SHARED-DEVICE PATH** — sub-profiles acting under an adult's authenticated session. *(This is also the reason naive `auth.uid()`-based RLS fixes don't work — see Security.)*
- **⚠️ THE COPPA SHIELD DOES NOT COVER THE OTHER DOOR.** A minor who joins **by code** has an email, a password, and a real auth row. The spec previously leaned on "no kid email, no direct collection" as a blanket COPPA advantage; that claim is false for the door strangers will actually use. A 13-year-old with their own phone and a join code is **normal traffic, not an edge case** — the join form has an adult/kid selector they will use. At 13 it is fine and at 10 it is not, and **nothing in the path asks.** Gate C item, already named there; what is new is that the shield is partial.
- **Adult profiles are PIN-locked, fail closed.** A PIN is *mandatory* at adult creation (with confirm-entry); existing PIN-less adults are intercepted and forced to set one. No path into an Adult profile without a PIN. This gives the approval gate teeth (anti-cheat).
- **Adult profile creation** routes through the `create_adult_profile` SECURITY DEFINER RPC.
- **PIN / password recovery (three locks):** (a) **adult-resets-adult** — a reset (↺) on each other adult's member card clears their PIN; fail-closed intercept forces a new one on switch-in. (b) **account-owner master path** — "Forgot PIN?" clears that profile's PIN via the owner-gated `set_profile_pin` RPC; closes the sole-adult lockout. (c) **account login** — "Forgot password?" → Supabase built-in reset → `/reset-password`. No new auth system, no new tables. *(Threat note: an acting adult can reset another adult's PIN — peer trust, fine for a 2-adult hold; revisit for peer/roommate holds.)*
- **A joining member's PIN is set AFTER an existing parent admits them, never during signup.** `complete_household_setup` always creates a joining member as kid/pending regardless of requested role, so calling `set_profile_pin` at join fired "Only parents can set a PIN" for every joining adult in every household. **The join path renders no PIN field at all.**
- **Hold membership:** an owner; an editable hold name. **Invite to the Hold** fires the Web Share API with a prefilled message + deep link `theemberhold.com/join?code=XXXX` (desktop clipboard fallback). **Join code visibility + invite are Adult-only.**
- The **"Whole hold" entity is marked by the keep glyph** in household-amber — members carry identity-color dots; the hold carries the brand object.
- **A hold has a timezone.** `families.timezone` (IANA, NOT NULL). The canonical clock for every household-local date — see *The household clock*.

## Registers and modules
**Registers are aesthetic. Modules are functional. They are not the same axis and they never gate each other.**

**The four registers — Forge · Garden · Keep · Hall** — are an avatar class and an ambient theme. A hearthmate picks one because they like it. **A member's register has no relationship to what their hold has enabled.**

> **SUPERSEDED (2026-07-25, same day):** the reading that the four registers were four functional layers. It failed decomposition. **Keep** (upkeep, chores, repair, DIY) is quests. **Garden** (yard, planting, outdoor maintenance) is quests. **Hall** (celebration, hosting, parties, feasting) is lists, campaigns and calendar events. All three are content and theme on machinery that already exists — which is what a register *is*. Only fitness failed to decompose into a quest, because it is the one household activity that **cannot mint embers without breaking constitutional rule 2**: a kid's ember requires adult verification, and nobody can verify a squat set. **Fitness is the odd one out, and it is the only module.**

**Module enablement is household-level, offered at the END of onboarding, and reversible by an adult from a Hold profile screen** *(LOCKED 2026-07-25)*. Stored as `families.enabled_modules text[]`, written **only** through the `set_enabled_modules()` RPC.
- **It is a binary and it is a BRANCH, not a toggle.** There is one module, so there is one question: Emberhold, or Emberhold with Forge. Selecting Forge does not set a flag and continue — it **launches a second setup flow** (equipment, experience, goals). That is precisely why it belongs at the end of onboarding rather than buried in settings: a settings toggle that fires a multi-screen wizard is the worst of both.
- **DESIGNED, NOT BUILT.** The branch screen (`/setup/intent`) is unrouted, unlinked, and has never been loaded by a human, and **nothing reads `enabled_modules`.** That is correct, not debt: module intent has nothing to point at until a module exists. **Parked with a trigger — finalized when Forge is built, not before.** Do not re-litigate it in the meantime and do not delete it; deleting and rebuilding costs more than leaving it.
- **The RPC is load-bearing and not a style choice.** `families.is_founder` is service-role-write-only, and that is what makes the paywall enforceable. Granting `authenticated` table-level UPDATE on `families` so a screen could write a feature column would have re-opened the paywall hole. The RPC means **zero new grants on `families`** — `authenticated` still holds column-level UPDATE on `name` only.
- **Never per-member** (constitutional rule 4).
- **Never offer a module that doesn't exist.**
- **Reversible.** Disabling hides surfaces; it never destroys data.
- **The column is correctly named as shipped and needs no migration.** `['household']` = base; `['household','fitness']` = base + Forge. A `text[]` for a current binary is mild over-engineering that costs nothing and stays honest if a second module ever appears.
- **⚠️ CORRECTION — the `'training'` → `'fitness'` string change is NOT free.** The prior claim that "nothing has ever written that value" is **false**. `set_enabled_modules()`'s allowed set still reads `'training'`, and any existing row carrying it must be migrated in the same change or the value becomes unreadable by its own RPC. Small, but it is a data migration, not a string edit.

---

## Architecture (the bones)
- **Everything is an "entry."** Shapes: quest, calendar event, list, list item, meal. Only **quests** carry the platform game.
- **The backbones:** the **calendar** (one timeline, constitutional) and the **ember economy** (every platform feature is a source, sink, or pool).
- **Three horizons:** Engine (daily quests), Campaigns (seasonal collective goals), Archive (the forever adventure log).
- **One spine for movement:** the **activity feed** (`activity_log`) — an append-only stream every meaningful state change writes to. The engine under notifications, wall callouts, and the Adventure Log.
- **The `+` is the universal capture.** Inside a create flow it transforms into that form's submit action. On a single-purpose surface that owns its own create action (a list's docked add-item bar), the `+` FAB is suppressed entirely.
- **Personal vs shared views are filters, not separate data.** *(Proven twice — the quest audience filter and the reward audience rail are both WHERE clauses, not second screens.)*
- **Role-aware home.** Adults land on **the Briefing** (operations), Kids land on the **game board** (play). Same nav slot, different surface.
- **Role-aware depth is filters, never separate systems — and the non-creator first run is the strongest proof of it yet.** Two audiences, two step arrays, one shell, zero duplicated machinery. See *Onboarding*.
- **THE MEMBRANE.** The platform game lives in **quests only**. Calendar, Lists, meals and notes are clean utilities that may *optionally* spawn a quest, and have no game at all. Gamify the chore, not the grocery item. Lists writes nothing to the activity_log spine.
  - **Module amendment** *(LOCKED 2026-07-25)*: **a module may carry at most ONE game of its own, sealed from the platform's.** The membrane exists to stop *every* surface becoming a game; "at most one per module, sealed" preserves that while allowing an optional module its own. **No module currently exercises this** — the Forge ships as a tool first. *(The amendment's original currency caveat is CLOSED: fitness cannot mint embers, so the amendment stands on its own and is contingent on nothing.)*
  - **Naming a utility is not touring it.** First-run may show Lists, Calendar and Campaigns by name; it may never walk a user into one. Seen, named, not used.

## The audience pattern (a first-class architectural primitive)
**A nullable/defaulted `audience` enum on an object + a viewer-role WHERE clause on its read query.** No second screen, no duplicated component, no parallel table.

- **`quest.audience`** — `anyone` / `adults_only` / `kids_only` (default `anyone`). Scopes an *unassigned/open* quest. The form control shows only for unassigned quests and hides the moment a quest is directed.
- **`reward.audience`** — `household` / `adults_only` (default `household`). Powers the couples rail.
- **The rule:** membrane-safe. Audience changes **visibility only** — never the sink, never the flow, never eligibility to earn.

Any future "X should only be seen by Y" want gets answered with this pattern before anyone proposes a new surface.

---

## The household clock (LOCKED 2026-07-23; the heal DRAFT 2026-07-27)
**The canonical clock for any household-local date is HOUSEHOLD-local, derived server-side from a single named function.** Not client-local stamped at write time, and never server UTC.

- **The mechanism:** `families.timezone` (IANA, NOT NULL) + **`household_today(fam uuid)`** (STABLE). Every expression touching `due_date` derives from it.
- **`CURRENT_DATE` is banned in any expression touching `due_date`.** It is UTC, the hold is not, and the gap is a whole timezone's worth of wrong for several hours a day.
- **Why not client-stamped:** `handle_quest_approval()` is a BEFORE-UPDATE trigger. It has no caller and no place to accept a client-supplied date, which structurally kills the stamp-at-write remedy. That the client-supplied-date trust-widening evaporated with it is a bonus, not the reason.
- **This is a class, not an instance.** Reported at one site; enumeration of `supabase/` found **six** live violations across two functions.
- **Capture SHIPPED 2026-07-26.** `complete_household_setup` validates the browser's `Intl.DateTimeFormat().resolvedOptions().timeZone` against `pg_timezone_names`, falling back to `America/Los_Angeles`.
- **⚠️ THE COLUMN IS WRITE-ONCE, AND THAT IS A DEFECT, NOT A DESIGN.** It is written only on the `families` INSERT in the create branch. There is no UPDATE path — not for a user, not for an admin, not through any RPC. **Two real consequences:** the thirteen pre-existing holds were backfilled with the Pacific default when the NOT NULL column landed on 07-23 (detection didn't ship until 07-26, so it never ran for any of them, and at least one hold is known to be outside Pacific), and any hold created while its owner is travelling is permanently wrong with no recourse. The 07-23 fix made the clock local; write-once makes it **permanent**.
  - *Reads as a bug and isn't: `profile.tsx` calls the same RPC, which looks like a path that could overwrite a correct zone with a traveller's browser value. It cannot — precisely because there is no update path. The absence of the feature is what makes the second call site safe.*
- **The remedy (DRAFT — contingent on the build landing and on verifying, on the glass, that a second sign-in changes nothing):** `families.timezone_confirmed_at timestamptz NULL`, plus a parent-gated SECURITY DEFINER `set_household_timezone()` validating against `pg_timezone_names`. A **set-once silent heal** fires only while `confirmed_at IS NULL`; after that, a human confirms and the system never follows a device again. An explicit parent-triggered reset button covers the once-a-decade relocation. **No table-level UPDATE grant on `families` is ever added for this** — `families` holds `is_founder`, and the 07-15 privilege-escalation fix stays intact.
- **THE RULE THAT GENERALIZES: a household's timezone is where the household LIVES.** Moving is a decision and travel is a trip, and no device signal can distinguish them, so the system stops guessing and a human confirms. Silent auto-correct on any parent login was **overruled and rightly** — a parent travelling for work would move the whole household's clock for a week, dailies would expire mid-evening, and nobody at home would connect the two.
- **Rejected, recorded so they stay rejected:** wiping the column to let it repopulate (**client-detected values only arrive when a client shows up** — Postgres cannot go get a browser API, and wiping produces thirteen NULLs while breaking holds that are currently correct); a UTC offset instead of an IANA name (offsets aren't stable, and the failure wouldn't look like a timezone bug — it would look like dailies rolling at 11pm twice a year); NTP (it answers "what instant is it," which nothing here is confused about); per-member timezones (**a chore is done at a house, not at a person**); a settings form alone (it builds a lever with nobody's hand on it — someone still has to know the right answer and go set it).

## The activeness model (LOCKED 2026-07-21)
**`isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts` is the single shared predicate.** Every surface that asks "is this quest live right now" imports it. None defines its own.

- **This exists because three surfaces each defined "active" independently** and drifted apart — a defect *pattern*, not a bug. Board, Briefing and wall are unified behind it.
- **Any new activeness surface imports the predicate or is wrong by construction.**
- **The pattern has recurred at least once since, in a different column.** Two derivations of *role* now exist — `FirstRunGate` reads `profiles.role` while `useMyProfile()` derives from `user_roles`. Same shape, same risk, unresolved. **Divergent derivations of one fact are the defect pattern; watch for them by shape, not by column name.**
- *(The suspected "fourth activeness surface" on the member profile / roster was killed by data on 2026-07-26. It was not a divergent predicate.)*

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
- **The anchor arithmetic DISCARDS the user's chosen day, by design — and the UI owes them that fact.** A monthly created on the 15th recurs on the 1st and nothing currently says so. **The recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** That stops being cosmetic polish and becomes the only place the app tells the truth about what it just did.
  - *Confirmed against live data: all four monthly quests produced exactly one successor each, all dated 2026-08-01, each successor's `created_at` matching its parent's `completed_at` to the microsecond. No duplicates, no orphans. The suspected clamp-and-drift failure (Jan 31 + 1 month = Feb 28, drifting down and never climbing back) is **structurally impossible here** because the day-of-month never enters the arithmetic — worth keeping on record because it is exactly what WOULD happen under the intuitive implementation.*
- **Spawn on approval only. There is no timer.** On approval of a recurring quest, archive the completed instance and spawn the next — **never reset in place**. One shared spawn path: `handle_quest_approval`.
- **The successor anchors to the household's today, not to the completed instance's old date.** Anchoring to the archived instance walks the whole series backward and was a real shipped bug.
- **Per-cadence rollover is asymmetric — and only the daily half is finished.** Daily **rolls forward on miss**: `roll_missed_dailies` archives the stale instance and spawns a fresh one for today — no ember accumulation, no guilt pile. Weekly and monthly do **not** roll, because approval is their only spawn trigger.
- **⚠️ CORRECTION (2026-07-26): an unapproved weekly or monthly sitting past-due forever is a DEFECT, not correct behavior.** One instance lingering is right — it is still owed. **Unbounded accumulation across cycles is a guilt pile, and it contradicts the exact principle daily rollover exists to enforce.** Two are stranded on the live board. *(The 2026-07-21 call that a weekly/monthly roll-forward job was redundant was correct for **approved** quests, where the trigger already handles succession, and completely blind to unapproved ones — a correct analysis of one branch applied to both.)* Build owed; the STALE chip predicate is likely the same fix.
- **`roll_missed_dailies` fires on every board mount, unconditionally.** Any create flow that navigates to the board runs it seconds later, so a freshly created daily is judged by it immediately. This is why its comparison must use the household clock: judged against UTC it declared same-day rows "missed," archived them, and respawned them dated tomorrow — correct for one round-trip, then clobbered.
- **Rollover has no health indicator.** A stuck daily and a healthy one are indistinguishable from the glass. Logged as an observability gap.

**Adult self-completion:** when the submitter is an Adult, completion writes `status='approved'` + `approved_by=self` **atomically** — embers mint immediately, never entering the pending queue. **The gate is *satisfied*, not bypassed** (constitutional rule 2). Kid completions unchanged.

**Two load-bearing triggers — do not drop or refactor without replacing the guarantee:**
- **`a_enforce_quest_update_authority`** — BEFORE-UPDATE, fires before the approval handler, restricts approval to adults. **The lock on the ember economy and the enforcement point for constitutional rule 2.** *(It gates on `has_role parent` and never compares `NEW.approved_by` to `auth.uid()`, so any parent session can stamp any adult. Under the walk-up trust boundary that is not a forgery risk — it is the mechanism that makes shared-wall approval possible at all.)*
- **`enforce_quest_family_refs`** — rejects any quest whose `claimed_by` / `assigned_to` / `approved_by` points outside the quest's own family. The cross-tenant guard.

**Quest creation is UNGATED, and that is a decision, not an oversight** *(LOCKED 2026-07-26)*. The RLS INSERT policy scopes to `family_id = current_family_id()` and `created_by = auth.uid()` and stops there — in explicit contrast to UPDATE and DELETE. **Approval is the real gate and it holds:** a kid can propose a 500-ember quest and cannot cash it, so the economy was never exposed.
- **What changes is the CURRICULUM, not the permission.** Onboarding never teaches quest authoring to a non-approver. **Absence, not a locked door** — a gate means a kid eventually finds a button that refuses them, which is the "where'd my chore go" failure aimed at exactly the person the app exists to be fun for. Absence teaches role by never raising the subject.
- *Known inconsistency, logged and harmless: `campaign.$id.tsx` wraps its create affordances in `{isParent && …}` while the global FAB and QuickAddTray do not. Two surfaces disagree about one rule. Removing the campaign gate is consistent with the curriculum call; adding a FAB gate contradicts it.*

**Planned extension (parked):** `objectives[]` — an array of checkable steps turning a quest into a **multi-step quest**. Steps don't mint; only turn-in does, and submit is locked until every step is checked.

---

## Onboarding (Pip-guided)
Pip, the ember-sprite mascot, is the app's guide. Pip is **guidance only** — read-only, never awards embers, unlocks, or gates anything.

**First-run is contextual setup, not a feature tour.** It is guided *doing*: the household performs the loop with its own names in it, so the pitch is **true** rather than claimed. Chores become quests, quests earn embers, embers buy real rewards.

### The shell
Every first-run flow renders inside one **`SetupShell`** — a step registry plus chrome. **The shell is neutral machinery:** it references no role, no creation state, no session state. That neutrality is what lets a single build serve every audience.

- **One-directional.** Skip is a single affordance **owned by the shell**, so no future screen can implement it wrongly, because no future screen owns it. Skip always exits to the board and never advances. **No back navigation** — nothing in the flow is destructive, and the accepted cost is that a typo'd quest title waits until the board.
- **Each screen writes on completion, never batched at the end.** Bail at screen 3 and the quest and the reward already exist. *(Verified on the glass.)*
- **Rejected:** an overlay tour with coach marks and spotlight state — that is a step-state machine you own forever.

### The creator sequence (SHIPPED)
`add-family → first-quest → stock-vault → the-hold → recap → add-to-home → board`

1. **add-family** — hearthmate repeater: name / avatar / role; Adult requires PIN. **Header speaks the task** — *"Who's tending the hold? / Your name, your face, and your role in the hold."* A task instruction, never a marketing headline.
2. **first-quest — the activation doorway. BUILT.** Pre-filled chips, "Light the quest." **The first quest may be assigned to a named hearthmate OR posted open to anyone** — assignment is visually primary, open-to-anyone is a full-width peer option, never a downgrade. Assignment is a preference and never a requirement, on two independent grounds: the board has an Open Bounties strip, so mandating assignment teaches a household that quests belong to *people* rather than that work can be *claimed*; and add-family is skippable, so a hold can legitimately arrive here with nobody to assign to. With no other hearthmates, default to open and say so in one line rather than presenting a choice that isn't one.
3. **stock-vault** — the sink half of the loop.
4. **the-hold** — Lists, Calendar and Campaigns: **named and shown, never explained, never entered.** No "tap here to make your first list," no entry point out of the flow. Its only job is to stop the mental model from setting as "chore app," so that three weeks later when the household needs a grocery list, Emberhold is a candidate. **This is a deliberate deviation from "setup, not tour," and it is reasoned — do not "finish the cleanup" by cutting it.** It ships with **labelled empty frames until real screenshots exist**; the placeholder state IS the shipping state and must read as finished, never as a broken-image box.
5. **recap** — "Welcome to Emberhold" (first run) / "welcome back" (replay only). Carries the flow's **terminal navigation** and the installed-state check.
6. **add-to-home** — the install tutorial. See *Installation*.

**Order is load-bearing.** The final thing a user reads has to be the thing they do next — ending on a feature overview ends on browsing.

**KILLED 2026-07-29 — the second loop recap.** A `handoff` screen restating the four-beat loop card a *third* time in one sequence was cut. **The loop card belongs to the landing page and on-demand help, not to first-run.** *(Mechanical warning, recorded because it nearly severed the flow: `handoff` carried the terminal navigation to the install screen. **Name every consumer of a route before removing it.**)*

**The historical gap is CLOSED.** "A new hold lands on an empty board with no path to a first quest" was carried for weeks as the top structural gap in the product. **It is built.**

### Installation (the PWA door)
**Install and standalone launch are delivered by the manifest and the iOS meta tags. NOT by a service worker.** The two were welded in a doc and are permanently unbundled.
- **Manifest:** `name`, `short_name`, `description`, `start_url: /`, `display: standalone`, portrait, 192 + 512 icons, `background_color: #1A110B` (an unset value flashed a white splash on launch).
- **iOS:** `apple-mobile-web-app-capable=yes`, `apple-touch-icon` → `/icon-180.png`, `apple-mobile-web-app-title=Emberhold`, `theme-color=#1A110B`, `apple-mobile-web-app-status-bar-style=black-translucent`. **An iPhone install launches a real standalone app, not a Safari-tab bookmark.**
- **The tutorial is ONE screen** riding the walkthrough that already exists — not a route, not a modal, not a state machine. *(Right-sized down from a nine-illustration four-branch sketch: out-habit, don't out-feature, applied to onboarding itself.)*
- **Its asset is a real captured share-sheet screenshot**, framed in a charred forge-window ember border — a photo of the actual sheet teaches a utility gesture better than an illustration, and a raw grey iOS sheet would otherwise read as a foreign object on ember-dark.
- **Platform-conditional copy**, a **webview-escape line** (*"Not seeing the Share button? Open theemberhold.com in Safari first"* — invite links open in Messages/Gmail webviews where Share→Add does not exist, and that is the single most likely silent failure for a cold install), and a **suppress-if-installed** check.
- **The service worker is a separate job and belongs to Gate B** — offline resilience, not presence. On iOS there is no `beforeinstallprompt` and an SW does nothing for install at all. **It earns its own careful pass:** a botched offline cache is exactly the silent-stale-content failure this project is paranoid about.

### The non-creator sequence (BUILT, DARK)
**The same `SetupShell` with a second step registry.** Not a separate artifact, and not the creator screens re-routed.

- **Why not re-route the creator screens:** of the three that write, **two are parent-gated** — `add-family` (`create_adult_profile` checks `has_role parent`) and `stock-vault` (RLS "Parents create rewards"). A kid walked into `stock-vault` eats an RLS denial behind a friendly mascot.
- **THE PROPERTY THAT MAKES ONE BUILD SERVE BOTH:** a joiner sets nothing up — the hold already has a roster, a board and a Vault — so every step is *teaching* rather than *doing*, and **a flow that writes nothing has no permission surface to differ on.**
- **Two arrays, split by role.** Adult: the board, approving, the Vault as operator, what the PIN is for, quest authoring. Kid: the board, claim/complete/submit, embers. **Authoring is ABSENT from the kid array — not disabled, not gated.**
- **The flow EXPLAINS the adult PIN and never collects it.** A fail-closed intercept already forces PIN creation at switch-in, and a joining member's PIN is set after admission. Collecting here would duplicate a working mechanism *and* be the only write in a flow whose entire structural property is having none.
- **Both non-creator doors are in scope** — join-by-code and profile-switch.
- **The role selector on the join form cannot corrupt the flow, by construction.** `complete_household_setup` writes `role='kid'` / `status='pending'` literally and files the selection in `requested_role` (advisory only). The confirmed role doesn't exist until a parent runs `admit_pending_member`, and the flow fires strictly after that. No guard needed.
- **DECLINED: telling a joiner what role they were confirmed as.** `admit_pending_member` sets `requested_role = NULL` in the same UPDATE that sets the confirmed role, so the evidence is destroyed at admission; `activity_log` has no role column; the admit toast fires on the *parent's* client. Rebuilding destroyed evidence to deliver bad news is the wrong fix twice over. **The demotion is a household conversation, not a system message.** What the app *does* owe: the parent sees what was requested at the admit moment, and **the kid flow never frames role as status.** *(Shipped — no `/first-run/*` screen says "you're a Kid" or compares roles, which satisfied the parked "retire identity-first labels" sweep on net-new strings at zero cost.)*

### The completion marker (the flow's one write)
**A per-profile DATABASE COLUMN.** Not derived, not client storage.
- **The trigger is a STATE CHECK on arrival in the authenticated layout** — placed immediately after the existing pending check. **Not** the create-vs-join fork in `auth.tsx` (a joiner is still `pending` and gets the waiting screen, so firing a walkthrough there teaches someone to claim a quest and parks them for a day), and **not** an event on the pending→active transition (admission frequently happens with the app closed; the user returns next day to a plain `/board` load with the event long gone — an event hook silently misses what is probably the majority path).
- **A derived gate CANNOT REMEMBER A SKIP.** Skip navigates to `/board`, `/board` re-evaluates, the flow re-fires forever — inverting the one-directional rule and producing exactly the hostage situation it exists to prevent. *(`FirstQuestDoorway` escapes this only because it is a dismissible banner with a sessionStorage soft-suppressor; a full-screen takeover cannot.)*
- **`localStorage`/`sessionStorage` are rejected outright** — on a shared device one profile's marker suppresses every profile's flow. The Vault-favorites persistence debt, repeating where it breaks the feature entirely.
- **Seam:** one read and one write function in `src/lib/first-run.ts`; every consumer routed through them.
- **⚠️ THE TRIPWIRE, AND IT IS NOT OPTIONAL.** `FIRST_RUN_MARKER_AVAILABLE`, `hasCompletedFirstRun` and `markFirstRunComplete` go live in a **single change**. Flip the constant while the read still returns a literal `true` and the route guard goes live against every walkthrough route — the exact regression this seam created and caught once already. **That belongs in the build brief, not in a code comment.**
- **SHIPPED 2026-07-29.** `profiles.first_run_completed_at timestamptz NULL`, backfilled to `now()` for every existing row in the same migration so no established household ever sees the flow. Written through a SECURITY DEFINER `mark_first_run_complete()` scoped `WHERE id = auth.uid() AND first_run_completed_at IS NULL` — single-row, idempotent, `search_path` pinned, `EXECUTE` to `authenticated` then `PUBLIC` revoked, **zero table grants on `profiles`.** Fires on both exits from the shell, terminal *and* skip. **Verified end to end on the glass: fresh creator and fresh join-by-code.**
- **The RLS question is ANSWERED and it did not matter.** `CREATE POLICY "Update own profile" … USING (id = auth.uid())` — a member *can* write their own row, so a direct write would have worked. **The RPC was still correct, for a reason nobody had at the time:** that policy is also `id = auth.uid()`, so **direct-write has the identical ceiling** and no client-side change gets past it. Only a SECURITY DEFINER function with a validated `profile_id` can ever mark a sub-profile. The chosen path extends; the cheap path was a dead end.
- **⚠️ THE MARKER'S READ AND WRITE ARE BOTH ACCOUNT-HOLDER-SCOPED, AND THEY MUST BE FIXED AS ONE CHANGE.** `FirstRunGate` reads the account holder rather than the switched-into profile; `mark_first_run_complete()` writes `WHERE id = auth.uid()`, and a kid sub-profile's row id is **not** any `auth.uid()`. A kid finishing the flow therefore updates **zero rows and returns success** — a silent no-op, not a wrong write.
  - **The two bugs currently cancel, which is the whole danger.** The gate reads the adult's marker (set), so the kid flow stays dark and nothing misbehaves. **Fix the read alone and you get the hostage loop:** gate reads the kid's NULL → fires → kid finishes → write no-ops → gate reads NULL again → fires forever.
  - **The fix is a validated `profile_id` parameter, family-checked server-side** — the same shape as the `actor_label` answer: a validated subject id, not a substitution.
  - **This is NOT the `isActiveQuest` divergence pattern, and calling it that would teach the wrong lesson.** That pattern is two derivations of one fact drifting apart. This is **one wrong assumption applied consistently to a read and a write, where the errors cancel and either fix alone breaks it.** Divergence announces itself; **cancellation hides.** Look for it wherever a read and a write share a scoping assumption.
- **The arrival gate must exempt EVERY walkthrough tree, not just its own.** The gate was written for joiners, who never touch `/onboarding/*`, so it exempted `/first-run/` alone. **The moment the marker went live, a brand-new creator — born NULL, role-agnostic marker, gate firing on every shell mount — was yanked out of their own setup flow into the joiner tour.** Shipped live and caught on the first fresh-creator walk. The exemption now names both prefixes literally.
  - **A hold creator IS a first-run-gate candidate.** The marker is per-profile and role-agnostic; nothing about creating a hold exempts you. *(Role-only routing at the gate is nonetheless correct, because a creator is marked complete by their own flow's terminal step or by skip and never reaches the gate un-marked.)*
  - **Residual, accepted:** a creator who bails mid-onboarding and returns to `/board` gets the joiner tour rather than resuming setup. Degraded, not broken, and strictly better than the pre-marker behavior — an empty board and nothing.

### Replay, and what does NOT fold into first run
**DECLINED — "Replay the intro" does not fold into the first-run flow.** The PipHelp sheet stays as it is and nothing points at the walkthrough routes.
- **THE SEAM IS MODALITY.** First-run is a full-screen routed takeover with shell chrome and a skip button — correct, because taking over the screen is the point. Replay is a bottom sheet, reached from a help affordance and dismissed. Folding means either replay becomes a full-screen takeover (far too heavy for "remind me how approval works") or first-run renders in a sheet (wrong for someone's first thirty seconds).
- Three smaller confirmations point the same way: opposite knowledge states, opposite exits, and the sheet works today. **The conflation was content with container.** The four beats are shareable; if they drift, *that* is when a shared source gets extracted.
- **The URL-reachability concern was never this item.** An established hold reaching `/onboarding/first-quest` by URL is a **routing guard**, closed by the completion marker regardless of what replay does.

### The Forge branch (DESIGNED, PARKED)
When Forge is built, the module question is **one binary at the END of hold setup** — Emberhold, or Emberhold with Forge — and selecting it launches a second setup flow (equipment, experience, goals). **The activation doorway stays last regardless**, so momentum carries straight into a populated board: a five-screen detour wedged between "post your first quest" and "see it on the board" breaks the one handoff that cannot afford it. **None of this is built** — see *Registers and modules*.

**Feature tour is on-demand** — offered only once the hold has content. The loop card (**"Post the work → Claim & complete → Approve & reward → Chase the campaign"**) doubles as the marketing pitch and is the intended centerpiece of the landing page.

### Signup (LOCKED 2026-07-26)
**Signup collects EMAIL AND PASSWORD ONLY before email verification.** Everything else — name, avatar, role, PIN, hold name, create-vs-join — is collected **after** verification at finish-setup. The pre-verification `localStorage` stash is **deleted**.
- **The mechanism was storage partitioning, not broken code.** The stash worked perfectly; `localStorage` is scoped per storage partition, so it can only be read from the context that wrote it. Returning to the original tab is definitionally the same partition and was always seamless — but tapping the confirmation link opens the mail client's in-app webview, a *different* partition, so the load returned null and the user retyped everything into a form the code already had. Same wall as cross-device signup, just further apart.
- **Accepted cost:** users who confirm in the same browser lose a no-retyping shortcut. Already the minority case. **One round of entry after verification beats two rounds where the first is discarded.**
- **Two items closed as side effects:** the adult PIN was written to `localStorage` in plaintext inside the stash and cleared only on success, persisting indefinitely on the failing path — **that security item closed by deletion rather than by fix.** And cross-device signup stopped being a special case, because there is no client-side state left to strand.

---

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

**⚠️ REDEMPTION APPROVAL MUST RECORD THE PIN-VERIFIED ADULT.** `approve_redemption` currently hardcodes `decided_by = auth.uid()` — the session owner — while its caller accepts an `approverId` it never sends. So Adult B approving on Adult A's session records A. **Quest approval's behavior is the correct one** (`wall.tsx` writes `approved_by: adultId`) and redemption moves to match it.
- **The fork was settled by copy written by someone who did not know it existed.** The shipped `/first-run/adult/pin` screen tells the user the PIN *"keeps an approval or a redemption tied to the person who actually signed off on it."* That is true for quests and false for redemptions. **The copy is the promise the product wants to make, and it was arrived at independently.** Fixing the string instead would ratify the weaker behavior by accident.
- **⚠️ THE DEADLINE IS BREACHED.** The rule was *"true before the arrival gate goes live."* The gate went live 2026-07-29 with the string still false. It shipped on a deliberate call — the breach is invisible in a single-adult hold and the joiner flow was worth more than an exact promise — **but it is now a live product making a specific claim it does not honor.** The screen names redemption explicitly, not vaguely. **First prompt of the next build window.**
- **Do NOT fix the string instead.** That ratifies the weaker behavior by accident and has to be unfixed later. The behavior moves; the copy is already right.

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
- **The rule protects the EMBER ECONOMY, and only that.** It therefore does **not** bind a Forge screen, which has no economy — see Part II.
- **The only semi-public Emberhold surface a non-customer can encounter** — which is why wall-visibility ranks cosmetic catalog items.
- *(The garage tablet is a different job: an **input** device at arm's length mid-rest, not a display glanced at from across the room.)*

---

## Navigation
- Bottom tab bar, **7 tabs**: BOARD · CAMPS · CAL · **LISTS** · VAULT · RANKS · YOU.
- The **"+" FAB is raised** to a kiss-overlap above the bar. It opens the create menu; becomes the docked submit inside a create flow; is suppressed on single-purpose surfaces.
- **Open question, currently DEFERRED:** seven tabs is already one past mobile comfort, and **it is not "add another tab."** Forge's contained-route posture — a gated `/forge` route with **no nav entry** — defers this entirely. The question returns only if a module ever needs to be discoverable.

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

**The empty seat.** A hearthmate added without an avatar renders as a silhouette in a frame. **In the picker and on the roster that is the honest state and is not auto-filled** — the roster is the one place a hearthmate says *"this is me,"* and stamping a default picks their identity for them. What is owed is a **nudge**: the empty seat is tappable and routes to the picker. **The wall is the one exception** — a silhouette is a dead spot on an ambient display, so the wall carries a render fallback. *(Open decision; jAIne recommended, not yet ratified.)*

### How to actually generate avatars
**The method lives in `playbooks/avatar-generation.md`.** Read it before touching a generator. Headline: the style block controls output by **axis, not length**; generate the **full roster in one run** so the anti-repeat constraint can bind; prompt the cool register through **material and light**, never supernatural vocabulary.

---

## Monetization (the Founding Guildhall)
- **One v1 SKU: the "Founding Guildhall."** A **one-time, household-level** purchase. Stripe Checkout + Supabase webhook + `families.is_founder` (**service-role-write-only** — an owner cannot self-grant it). Staying outside app-store rails retains **~97%** of revenue.
- **What it unlocks:** the paid share of the avatar roster. Free keeps **16** (four per register); the Guildhall opens the remaining **32**. The gate is a DB value (`system_flags.founder_gate_enabled`) — flipping it is an UPDATE, not a build.
- **The free tier must be genuinely COMPLETE, not a demo.** Every free four needs a genuine kid-magnet.
- **Household-level, always** (constitutional rule 4).
- **Membrane-safe cosmetics.** A paid avatar is not a better avatar.
- **The catalog is retention leverage, not acquisition insurance.** A retained household becomes worth $25 + $10 + $5 rather than $25 flat. **Wall-visibility ranks catalog items.**
- **Next catalog item:** living-hold ambient themes, one per register, canvas particle-based. **Keep first**, as the performance proving-ground.
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
- **A foreign asset gets a frame, not a redraw.** A captured screenshot, a system sheet, or any borrowed rectangle dropped onto ember-dark reads as an object from another app unless it is bordered into the world. **Frame it (charred forge-window, glow bleeding inward, top-edge catch-light) rather than re-illustrating it** — the real thing teaches better and costs no visual-lane work.
- **Brand: two marks, one keep.** App icon (round-towered keep + glowing hearth on a charred tile) = universal small mark; login crest (the same keep in a forged-gold heraldic shield) = ceremonial mark. Motto **"by hearth & hold."** Plus **Pip**.
- **A register's palette may never override the ember-lit base or the brightness rule.** Registers are a skin on one house.

## Experience layer (the juice)
- Completing a quest is theater (Battle Chess principle). **Tier = intensity, register = theme — no new data model.**
- **Reactive micro-motion:** ember burst by tier, approval echo, new-approval pulse, count roll-ups, press feedback, breathing submit button, plus a subtle state-driven household-warmth shift.
- **AI-generated, never stale:** grow a library of themed vignette assets in the background, cache, select instantly at the win. Bake an alpha-check into the pipeline. *(Generate offline, select locally, instant on the glass, zero marginal cost — the same shape Part II's content factory uses.)*

## The activity feed (the event-log spine)
A single **append-only, permanent, immutable** stream.
- **`activity_log` table.** Fields: `id`, `actor_id` (soft, nullable), `actor_label` (frozen), `verb` (enum), `object_id` (soft, no hard FK), `object_label` (frozen at write), `ember_delta` (nullable), `campaign_id` + `campaign_label` (nullable), `significance` (**Legendary** override only), `created_at` (indexed).
- **`logActivity()` helper** — the single app-level write path. No DB triggers.
- **Five curated write points:** `quest_approved` · `bounty_posted` · `reward_redeemed` · `campaign_completed` · `event_created` (ember_delta null — membrane).
- **CAP THE READ, NEVER THE WRITE.** The table is permanent. **No pruning queue, ever.**
- **The identity fields are already server-derived, and the exposure is smaller than this doc used to claim.** `actor_id` derives from `auth.uid()` and cannot be faked from the client; `family_id` derives from `current_family_id()`; `ember_delta` is nulled for non-parents. **Only `actor_label` — the rendered display string — is client-supplied.** So the exposure is a feed that can render a wrong *name* over a row that still records the truth: a cosmetic-layer lie with auditable data beneath, intra-household under the walk-up boundary. *(It is also the ONLY client-supplied identity field in the entire migration set.)*
- **⚠️ DO NOT DERIVE `actor_label` FROM `auth.uid()`. IT IS A REGRESSION, NOT A FIX.** Four of six `logActivity()` call sites **deliberately** pass someone other than the caller — a parent approving a kid's quest must show the *kid's* name. Deriving from the session would rewrite every completed-quest feed entry to the approving parent. *(This prescription sat in this doc for three sessions on a summary nobody re-derived. **Severity in a doc outlives the evidence for it.**)*
- **The column is doing two jobs** — sometimes "who clicked," sometimes "who gets credit." The likely shape is a validated `subject_profile_id` with the label derived server-side from it: **a third field, not a substitution.** Open design call, not a bug fix.
- **The feed is where an optional module touches the platform.** A module may write to the spine — that is presence, not economy. Same theater, no exchange rate.
- **Downstream consumers:** the notification RECENT panel · the full in-app feed view · wall/kiosk callouts · the **weekly recap** · the Adventure Log · the family message board.

---

## Security posture (design truth, not just a bug list)
- **The core rule: embers mint only on approval by an adult** — constitutional rule 2, enforced at the trigger layer.
- **The tenant boundary holds under authenticated cross-tenant attack (VERIFIED 2026-07-19).** All cross-tenant reads/writes, `current_family_id()` integrity, every household-scoped SECURITY DEFINER RPC, the `family_xp` view, the insert trigger, and `system_flags` write are SECURE.
- **The table-grant surface is CLOSED (2026-07-26).** `anon` holds **zero** privileges on every table in `public`; `authenticated` is reduced to SELECT/INSERT/UPDATE/DELETE.
  - **The reframe that matters:** this was never fourteen instances of drift — it was the Supabase default (GRANT ALL to `anon` and `authenticated`, with RLS as the intended gate). So the real question was "is RLS sound against `anon` on all fifteen tables," and it is: all fifteen RLS-enabled, exactly four anon-reachable policies, all RESTRICTIVE false denials.
  - **The revoke was still correct, and this is the part worth remembering: RLS gates SELECT/INSERT/UPDATE/DELETE and nothing else.** TRUNCATE, TRIGGER, REFERENCES and MAINTAIN were held by `anon` on fourteen tables with nothing whatsoever in front of them. Not reachable through PostgREST, so theoretical — but **"RLS is the sole gate" was only ever half true**, and that is the kind of sentence that ages badly in a doc.
  - **Why the revoke was safe:** pre-login flows route through SECURITY DEFINER RPCs that execute as owner and ignore table grants entirely. Verified from `pg_class.relacl`, not from the migration, and confirmed on the glass.
- **Load-bearing grant rules (LOCKED — do not regress):**
  1. **`member_spendable()` is family-scoped.**
  2. **Quest FK columns resolve to the quest's family** — `enforce_quest_family_refs`.
  3. **`families.is_founder` is service-role-write-only.** This is what makes the Guildhall entitlement enforceable.
  4. **`founder_gate_enabled()` / `my_household_is_founder()` execute is `authenticated`/`service_role` only.**
  5. **Module enablement writes only through its RPC** — no table-level UPDATE grant on `families` is ever added to support a feature column. Adding one would re-open rule 3.
- **GRANT DRIFT IS DOWNGRADED FROM A RECURRING CLASS-LEVEL DEFECT TO A BOUNDED PLATFORM RESIDUAL** *(2026-07-26)*. Six instances over three weeks were all one mechanism: Postgres re-grants by default on newly created objects, and every prior fix was a reactive one-off against a default that re-asserts itself. **The 07-26 migration altered default privileges for the `postgres` owner** — the first fix aimed at the mechanism instead of the symptom.
  - **What remains and why it can't be fixed here:** the `supabase_admin` default-privilege entry is unchanged, because `postgres` is not a member of that role. It fires only for objects `supabase_admin` itself creates — extensions and platform-managed schema work — never for this project's migration path. Unreachable from this connection.
  - **The standing rule changes shape.** It is no longer *"revoke reactively, forever."* It is **"re-read table grants after any Lovable platform-level change."** Much cheaper, and bounded for the first time.
  - **Convention when creating: grant `authenticated` first, then revoke `PUBLIC`. Order matters.**
- **Hand-applied DB changes are forbidden.** Every change lands as a migration file. Structural changes verify against the **live** surface (`information_schema`), not migration history.
- **Schema is undone FORWARD** *(LOCKED 2026-07-25)*. A Lovable version-revert restores code and project files **only**: applied migrations are not rolled back, objects stay live, and the migration *files* are removed while their objects remain. There is no mechanism to roll back a specific migration; undoing schema means writing a new migration that reverses it. Drop in FK order **without CASCADE**. Run a read-only live-schema recon before any teardown.
- **Join codes admit members to a household — gated by admit-on-approval.** A join-by-code creates a `profiles.status = 'pending'` profile; **`current_family_id()` returns NULL for any non-active profile**, so every family-scoped RLS policy denies by construction. The self-selected role is stored as `requested_role` (advisory only); **no `user_roles` row is written at join.** A parent admits *and* confirms the role in one action (`admit_pending_member`). **Do not refactor `current_family_id()` without preserving the NULL-for-pending guarantee.**
- **The walk-up trust boundary (accepted, intra-household).** In the shared-session model a device-kid holds the owner's ambient parent JWT; the PIN is the only kid/parent line and it is client-side. **Intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. **Three findings are ignored or downgraded *because of* this boundary**; deciding the own-session-vs-per-member-auth fork revives all of them. The fork is parked, and four open items wait on it.
- **Kid-vs-kid impersonation.** The client-side profile switch is ungated. Kid PINs stay **off by default** (they tax the walk-up thesis). Mitigation already built: the **redeemer's name + face on the adult approval card**.
- **Analytics is a disclosure surface, not a bug.** A first-party tracker (`flock.js`, self-proxied via `/~api/analytics`, most likely Lovable-injected) fires in the app `<head>`. **It must be named truthfully in the Gate C privacy policy** — minors are genuine users. Not a faces problem: there are no photographs anywhere; avatars are illustrated.
- **Persistence debt:** Vault favorites use `localStorage`. Fine for validation, fatal for the shared-wall model.

---
---

# PART II — THE FORGE (the fitness module)

**Scope: the Draper household. Status: designed, not built.** Option A's shape is settled and written here as design truth. **Option B remains the only open fork.**

**Names:** stored value `fitness`; product name on screen **Forge**.

## Scope, and what it changes

**It is a module inside Emberhold, not a sibling app** *(LOCKED 2026-07-25)*. A separate product, tenancy, and domain is DECLINED. The standalone frame reached a domain recommendation, an eight-table schema, and a working slice before it broke — on **proportion**: it had grown a tool wall, a metal-composition system, and project-scoped blocks while still being unable to log a working set. Merging fixes the ratio and deletes work: no second auth, no second tenant bootstrap, one member table, one PIN system, one avatar pipeline, one Founding Guildhall unlock.

**It is scoped to ONE HOUSEHOLD, not to strangers** *(LOCKED 2026-07-27)*. Emberhold has a thin market and real teeth. The fitness market is the opposite — saturated with competent products, so the bar for a stranger-facing Forge is far higher and the ceiling lower. **Don't dilute the "wow" of Emberhold with a "meh" Forge.**
- **Rebuilding it standalone for two users is REJECTED.** That reverses a decision made on grounds audience does not touch — the merge was decided on infrastructure, and every one of those costs stays fixed while the audience shrinks to two. **Standalone-for-two is a worse proportion than standalone-for-strangers.**
- **"Forge dilutes Emberhold" is structurally impossible.** `enabled_modules` plus a gated route with no nav entry means a stranger household never renders it. **The real risk is SUBSTITUTION, not dilution** — a Forge session happening *instead of* a Gate B item rather than alongside one. That is the tripwire.
- **Money survives intact.** ~$192/yr of Fitbod cost avoidance counts identically to $192 of revenue against the $636/yr tooling target.

**The bar is a USER TEST: Scott and May stop opening Fitbod.** *("A full Fitbod replacement" is RETIRED as the standard — read literally it has no exit condition, since Fitbod has a funded team and an edge-case surface Forge will never match. That standard made A permanently unshippable, the opposite of what it intended. Read as a user test it is achievable and probably closer than it looks, because Forge serves two known users in one known gym with no edge cases.)*

**Why it is the only module.** Every other candidate area decomposes into quests, lists, or campaigns. Fitness does not, because it **cannot mint embers without breaking constitutional rule 2**. **It does not mint, and it has no economy** — which is also why the wall's never-mints rule does not bind a Forge screen.

**Containment posture:** a route at `/forge`, gated on `families.enabled_modules` containing `'fitness'`, **with no nav entry.**

**Explicitly out of scope:** client-facing training (paying clients, not hearthmates). A client is not a hearthmate and cannot share a household tenant.

**Platform constraint, and it is hard.** Heart-rate-validated work requires Apple Health / Google Fit. **There is no HealthKit web API**, and the PWA posture is LOCKED. **Self-logged work is the only modality a PWA can honestly measure** — but *validation* is the only thing missing, not participation (see *Cardio*).

## v1 is a PRESCRIPTION ENGINE with a log attached

**Not a logger with programming bolted on.** Asked directly what they valued about Fitbod, both users named *what exercise to do* and *what weight to use*; Scott added the rest timer. **Neither named logging.** The log is the commodity — every free tier does it well and nobody switches apps for it.

**Therefore trust in the instruction IS the product**, and the incumbent's most-cited weakness is that it won't explain itself. **The reason string stops being a nice touch and becomes the moat**, sitting directly on top of the #1 feature. *"3×5 @ 185, avg RPE 6.7 — all sets complete under target, adding 10 lb."*

**The reason string stays COMPUTED, never narrated.** It is trustworthy precisely because it is derived. A model may pick exercises and structure; **it may not narrate math it didn't do.**

### The pre-session gate
**One screen, a few taps, before generation.** `progression.ts` autoregulates on RPE, which is a **backward** signal — it learns a session was too hard *after* it's been done and corrects one session late, on exactly the day it was needed. **Nothing in the current asset accepts a constraint before deciding.** That is a real gap in the half we assumed was finished.

- **READINESS IS A SCALAR. INJURY IS A FILTER. Two controls, two lifespans, never one dial.** *"I slept badly"* dials the whole session down and expires tonight. *"My shoulder is hurt"* means never prescribe this movement at any weight, and it is still true next Tuesday. Jammed into one "how are you feeling" dial, the result is wrong for both. **A unified wellness/readiness score is REJECTED.**
- **An injury flag carries its own resolution, asked at flag time:** *reduced load* or *avoid entirely*. One extra tap deletes an entire severity-inference problem and keeps the call with the person who has the shoulder. **Note the cost asymmetry:** reduced-load is nearly free because the load math already exists; **avoid-entirely is the expensive branch, because avoiding creates a hole something must fill — and that is what makes the catalog non-optional.**
- **Expiry rides the training split, not a timer.** Flags don't decay; they get re-asked at the next session touching the flagged region — *"last push day you said your chest was hurt. Is it still injured?"* **The split IS the timer.** The question is **three-way — still hurt / better / cleared** — because "better" is the common real case and a binary forces a lie. *"Better" moves avoid → reduced*, reusing the fork rather than adding one.
- **Inferring readiness from wearables is REJECTED** — no HealthKit web API, and a *declared* rough night is more honest than an inferred score, because the user owns the result instead of arguing with it.
- **SCOPE GUARDRAIL:** one screen, three taps. **The moment it grows a body map, a pain scale, or a history graph, it has stopped being a prescription input and become a wellness tracker** — a different product.
- **The engine has no forward input path yet.** It reacts and cannot be told anything in advance. Scope this with rep-compensation; they are the same gap.

### The engine
**Pure client-side TypeScript. Progression logic never lives in Postgres** *(LOCKED 2026-07-25)*. `src/lib/progression.ts` exists today: RPE-driven autoregulation, bounded subset-sum plate-snapping against a finite inventory, Epley+RIR e1RM, `"timed"` equipment returning null, 10/10 tests, zero Supabase imports. It survived a full eight-table teardown intact, which is what having no Supabase imports buys.

- **⚠️ CORRECTION (2026-07-28): the rule does NOT rest on connectivity.** This doc previously stated the engine lives client-side *"for one non-negotiable reason: a garage has unreliable connectivity."* **For a bespoke two-person build, connectivity is never a concern.** The rule survives on better grounds: the engine is already written and tested (deleting it costs a rewrite; keeping it costs nothing), and **even on perfect LTE a rest timer and a plate calculation over the network is worse than one running locally — mid-set is the least forgiving moment in the app.** Local isn't a hedge there; it's just faster.
- **Two real unlocks follow from dropping the connectivity assumption:** the exercise catalog can live in **Supabase rather than as a static asset**, editable without a redeploy — which matters enormously while the substitution map is being tuned — and **live mid-session regeneration becomes viable** (*"rack's taken, give me something else"*).
- **The connectivity clause is rescoped, not dead.** It returns as a *product* constraint the day this meets strangers with basement racks and dead-zone commercial gyms.
- **Tests are mandatory here specifically** — a silent off-by-one corrupts training data that cannot be reconstructed.
- **⚠️ THE ENGINE HAS NO PROGRESSION AXIS EXCEPT LOAD.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo, not stubbed. **It cannot express *"175 instead of 185, so give me 8 instead of 5"*,** which is now half the named differentiator. Rep-compensation is **the reason string running in reverse, and it is where Fitbod goes silent.** It needs a **validity floor**: below some deviation the app must say *"that's a different exercise now"* rather than compute an equivalence it doesn't have.

### The exercise catalog — the single blocking dependency
**Movement pattern · muscle attribution · equipment requirement · substitution map.** Four features ride one tagging layer: injury substitution, rack-taken swaps, resequencing, and the pre-session gate's relevance check. **Everything good in the module is downstream of it.**

**Generated offline, reviewed by Scott, shipped as data.** **Make is a content FACTORY, never a runtime.** As a runtime it fails on latency, on per-operation quota, and on constitutional rule 7's guardrail — if a feature could run deterministically and a model call was added anyway, that is the rule being gamed against itself. As a factory it buys the most expensive piece of the module in one pass. *(Same shape the experience layer already uses: generate offline, select locally, instant on the glass, zero marginal cost.)*

**Core is programmed as real work, not accessory filler.** The incumbent files core as accessory and always will; ours doesn't have to. A content decision inside the template library, costs nothing, and is a visible "we're not that app" signal in week one — from a user who articulated the complaint unprompted.

### The session model
- **Contention resolution is a first-class feature, and no competitor could ship it.** It requires two people in one tenant sharing one equipment inventory, generated together — Fitbod structurally cannot, because it has no concept of the person twelve feet away. Constitutional rule 5 already has the module reading both members from the existing table.
  - **The ladder: RESEQUENCE → SUBSTITUTE → MODIFY LOAD.** Resequencing within your own session costs zero workout integrity (it's still your workout, reordered). Substitute only when nothing else is doable. Modify load last. **Most collisions die at tier one for free.**
  - **Bias at generation, never schedule.** Both sessions plan from one call and openers are staggered so nobody starts at the same rack. Near-zero cost, kills a chunk of collisions before anyone leaves the house.
  - **Equipment presence detection is CUT ON SIGHT.** The users are ten feet apart and can see the rack. One button beats any sensing, and anything that tries to detect presence will be wrong at the exact moment someone is standing there sweating.
- **A time budget is a generation INPUT, not a schedule.** Its real job is **SIZING, not prediction** — five exercises instead of eight. Accuracy is a bonus. Minute-by-minute planning is killed: real sessions drift and any timeline is wrong within ten minutes.
  - **Duration estimates calibrate from the user's own rest-timer data**, free. The app observes real set-to-set intervals including setup, plate changes and conversation, per person, over months. Fitbod estimates from population averages; ours estimates from these two people. **A byproduct of data already being collected, not a feature to build.** It also extends the reason string past load into session shape: *"45 minutes — keeping both compounds, dropping the second accessory."*
- **NAG WITH THE REST TIMER; NEVER WITH SESSION DURATION.** The principle generalizes past Forge: **interrupt only when there is an action attached.** "Go" is a thing you can do. A duration alert is a verdict you cannot act on, because you cannot lift faster — it turns a training tool into a stopwatch with anxiety. At most one soft prompt near the end.
- **The rest timer is derived, never stored.** **No PIN on set logging.** **Attribution, not concurrency, is the real risk.**

### Data & units
- **Weight is stored in POUNDS.** Most American gyms mark plates and dumbbells in lb and `progression.ts` already names every field `_lb`. **Kg is not a display toggle on lb data** — a standard bar is 20kg (44.09lb, not 45) and kg plates don't map onto lb denominations, so the achievable load set genuinely differs. Store one unit, convert at the glass.
- **Dumbbell load is logged PER HAND**, matching Fitbod so numbers stay portable and avoiding mentally halving at the rack every set. **This forces a `bilateral` flag on the exercise row** — a 40lb bilateral press and a 40lb single-arm row are the same logged number and twice the work apart. **That flag cannot be added after data exists** without every historical volume figure silently changing.
- **A dumbbell e1RM must never be estimated from a barbell e1RM or vice versa.** 40 per hand is 80lb moved and is not an 80lb bench.
- **`prescribed` and `actual` are two fields, never one.** Autoregulation reads `actual`, always.
- **Cardio is IN**, self-reported, and **prescribed rather than autoregulated.** *(The conflation to avoid: "a PWA cannot VALIDATE cardio" is not "cardio cannot be INCLUDED." Time and resistance are self-reported exactly like every other number in the app.)* Cardio entries carry duration and optional resistance, nothing computes them, and **there is no reason string because there is no reasoning** — honest and sufficient.
  - **A session must hold ENTRIES OF TWO SHAPES and allow MIXED sessions from day one.** *"10 minute bike warmup"* is the front of a lift day, not its own session; if cardio only exists as a separate session type the warmup gets skipped or creates junk data. **Retrofitting the second shape after the first has data is the expensive version.**
  - **Bands are out of v1** — same category as cardio (equipment producing no load set). Once a non-load entry shape exists they stop being a second engine and become a rank plus reps. **Door unwelded, not opened.**
- **DECLINED for now: a session-RPE field for cardio progression.** Do not store a column until something reads it.

### Equipment
**Standards-plus-override.** Users select equipment **TYPES**; presets generate the load set. **No plate-by-plate inventory entry** — a real user will not enter every plate they own.
- **Dropping inventory entirely is rejected** — the engine needs to know what loads exist or plate math is guesswork. **The inventory is not the mistake; making a user type it in is.**
- **THE TEST: if Scott's gym cannot be expressed as preset + overrides, the preset model is wrong.** His rack is unusual enough to be a genuine stress case. *(The failure mode being guarded against: building the inventory system with one gym as the schema rather than as a row in it.)*
- **When the prescribed load isn't achievable, the user adjusts manually and the app compensates in REPS** rather than modelling finite plate counts.
- **⚠️ Equipment records need `exclusive` vs `shareable`.** A Smith or squat rack is exclusive; dumbbells at different weights are parallel. One field, and contention resolution means nothing without it.
- **⚠️ The Smith machine offset is ASSUMED, not measured.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

### Display mode (the garage screen)
**A separate always-on device that IDLES AS THE WALL and is taken over by a workout.** Not a toggle of the existing wall mode, and not a new mode either — **precedence.** Manual "start workout" / "end session"; no presence detection.
- **The device posture is genuinely different:** the wall is glanceable and passive; a Forge screen is **touched, wet-handed, between sets.** Framing it as a toggle would get it built as a wall skin, which it isn't.
- **THE AVATAR IS THE SESSION LANE.** Tap an avatar → pre-session questions → Start → the lane expands into an agenda line carrying exercise, load, reps → tap the item to log a set → the rest timer appears. **Multiple lanes run concurrently. This is NOT split view** — it is the existing roster component doing double duty, exactly what constitutional rule 5 prescribes.
- **A free feature falls out of it:** with both agendas visible side by side you see a collision coming two lines ahead, so contention becomes avoidable on sight rather than resolved after the fact.
- **It removes the iOS background-timer problem entirely** — an always-on screen never suspends. A real technical argument, not a nice-to-have.
- **A second screen in a daily-traffic room is more surface for the habit to live on**, which is the existing retention thesis.
- **The wall's never-mints-never-approves-never-edits rule does NOT bind here.** That rule protects the ember economy, and Forge has none. A Forge screen that writes sets operates outside the surface the rule protects.
- **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**

### The LLM seam
**Generation happens at SESSION START, not per set. The model writes the plan; the client executes it.** Once the session is materialized as a cached object, the in-gym loop is entirely local — sets, rest timer, load adjustment, logging.
- **The API key lives in a Supabase edge function, never the client.** theemberhold.com is public and a key in the bundle is a key on the internet. **This is the one place bespoke buys no shortcut.**
- **The deterministic template path stays as the no-generation fallback** — not extra work; the same engine doing double duty.
- **The real unlock of bespoke is the CONTEXT, not the model:** two people's full training history, actual equipment, standing injuries, and individual response to volume, with no consent flow and no privacy surface. **That is a prompt no product could ship.**
- **Open:** whether the LLM plans both sessions in one call or one per person. **Contention effectively requires one call** — confirm that's intended before it becomes an implicit constraint.

### Participation
**"Adults only" is a proxy for "trains" and it will break** — a twelve-year-old lifting in a garage gym is normal. **When it breaks the answer is a per-member flag, not a role check. Do not build it now.**

## The open fork — Option B

**Option A is v1 and is described above.** Option B is the only thing still undecided.

**Option B — TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as a collection that is legible evidence of what was actually trained. **No currency in the fungible sense — an artifact isn't *worth* anything, it's *evidence*, which makes the seal structural rather than policed.**

**What B owes before it could be written as truth:**
- Whether materials **pool** or are **claimed at commitment**.
- What materials are typed **on**. Typing by modality contradicts the locked call that all modalities produce equal value — and with one available modality there is nothing to type across anyway.
- Whether a collection survives the calibration check: *this is a family chore game with your kids' faces in it.*

**THE STANDING RISK, named because it has already happened once.** The game is the most interesting part to build and the tool is the part that pays. **Option B does not get designed until Option A has been used for a month.**

## What does NOT come along at stranger scale
*Filed so it isn't discovered late. None of it changes a line of what gets designed today.*

- **⚠️ PRESCRIBING AROUND A DECLARED INJURY IS A DIFFERENT ACT WHEN THE USER ISN'T YOUR WIFE.** For the Drapers it is two adults making a call together. For a stranger it is an app that heard *"my shoulder hurts"* and told them what to lift — **a liability posture, a disclaimer surface, and possibly a scope cut.** The single best feature in the design is the one most likely to need a lawyer before it meets strangers.
- **The equipment model is one garage with a known rack.** A commercial floor has occupied racks and shifting availability, and *"what equipment do you have"* stops being a settings screen. Annoying, not structural.
- **The connectivity clause comes back** — basement racks and dead-zone commercial gyms are real. The client engine already covers it; the LLM generation path would not.
- **Forge display mode is a semi-public surface.** Injury flags and body-weight numbers ambient in a room guests walk through. Fine in the Drapers' garage.
- **The free/paid split reactivates** (constitutional rule 7), plus quota and cost-control logic a two-person build doesn't need.
- **Garage hardware is cold, dusty, and humid.** Obvious in July, annoying in January.

---

## Tech & working model
- **Stack:** Lovable.dev (React + Supabase), mobile-first installable PWA. **Stripe Checkout + Supabase webhook** for the Guildhall. **Copilot Create** for avatar/asset generation (work account — zero Lovable credit burn). **Claude Code** for the codebase-reading jobs Lovable can't do from inside itself.
- **The lanes (LOCKED):**
  - **Scott** — vision, taste, final decisions. **Anything whose success criterion is visual.**
  - **jAIne** — hears, challenges, breaks down, translates intent into direction. **Cannot see. Does not brief visual work as if she could.** *Does not reach into Lovable's lane.*
  - **Lovable** — engineers the functional output. Default lane for frontend and live-DB work.
  - **Claude Code** — text: code, config, strings, files, structure. **Syncs to `origin/main` before reading.**
  - **The edge of Code's lane:** the moment the success criterion is *"does this look right,"* Code is outside it — and briefing harder does not help, because the briefer is blind too.
- **SESSION LANE IS DECLARED AT OPEN.** Four values: design-only · design + Lovable · design + Code · full. **Credits and hands are INDEPENDENT switches** — a desktop with twelve credits left is not a build lane, and Lovable runs fine from a phone. Only Claude Code is genuinely desktop-bound. **Design is always on**, and rest-period design is a real and productive mode.
- **One writer at a time.** Data-layer / live-DB → Lovable. Frontend text/code → Claude Code. Never simultaneous.
- **Brief the floor and the intent; leave latitude on execution.** **jAIne's failure mode is over-specifying when under-trusting downstream.** *(Exception: high-stakes live-DB audits get a directive, precise roadway.)*
- **RIGHT-SIZE THE ARTIFACT TO THE TASK.** A three-tap gesture does not need a nine-screen state machine. **The bloat instinct wears a thoroughness costume.**
- **Fetch the canon before producing anything.** Not ceremony — it is what catches an invented noun in thirty seconds instead of eight tables later.
- **Recon before build. Brief recon to DISPROVE, not to confirm.** A brief that asks "confirm X" gets X — and recon that kills your own recommendation is the mechanism working.
- **Sweep the class before fixing the instance.** Seven functions carrying a grant default; six date sites across two functions; one sampled table with wide-open grants implying fourteen unread ones.
- **Decompose before you promote.** A thing that looks like new architecture may be existing machinery wearing a name. Three of four proposed functional areas dissolved into quests and lists under inspection, in the same session they were proposed.
- **UNBUNDLE WELDED ASSUMPTIONS.** "Service worker = installable PWA" was two jobs fused in a sentence, and fusing them would have delayed a shipped feature behind an unrelated one.
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.** A redundant-looking screen carried the terminal navigation for the flow that followed it.
- **CHECK THE DEPLOYED COMMIT BEFORE DIAGNOSING A MISSING FEATURE.** Stale local bits mimic an un-built feature perfectly.
- **RE-DERIVE A LOCKED RULE'S REASON BEFORE USING IT AS AN ARGUMENT.** Quoting a locked rationale is not the same as testing whether it still applies. **Severity in a doc outlives the evidence for it.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.** Read what landed against what was briefed, every time.
- **A defensive layer is not verified by the migration that adds it.** Fix → re-read the catalog → confirm on the live artifact. **HTTP 200 is not "renders." A PREVIEW IS NOT PROD.**
- **"Shipped" means a completed end-to-end loop, verified on the glass.** **Design-complete ≠ shipped.**
- **A Code job isn't done until the artifact is observable from outside the agent.** Verify `origin/main`, not the agent's report.
- **STATE LIVES IN THE REPO OR IT DOESN'T EXIST.** A decision that only exists in a chat log is a memory, and the next session doesn't have it. The most expensive instance so far cost an entire onboarding design, rebuilt wrong two weeks later from a one-line compression.
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit and P4×L8-class work, and the jAIne seat).**
- **The calibration check:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that is information.

## The other docs
`north-star.md` (why + the gate ladder — read first, cold) · **this spec** (what it is) · `status.md` (where the build is) · `parking-lot.md` (what might be) · `decisions.md` (what we decided and why) · `playbooks/` (how to do a thing) · `archive/` (reasoning history). Full manifest: `canonical-manifest.md`.

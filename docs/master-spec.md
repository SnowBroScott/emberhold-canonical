# Master Spec
**What Emberhold IS.** Canonical design truth — above any individual build prompt or chat. Status lives in `status.md`; this doc does not track it.

Last substantive update: **2026-07-30** — the redemption-approval fix shipped, and it **overturned a prescription written in this doc.** Redemption approval now records the PIN-verified adult through a validated optional parameter; `quests.approved_by` turned out to be the *weaker* of the two paths, not the model to copy. Also folded: the two-kinds-of-adult finding (`create_adult_profile` mints profiles with no `auth.users` row, so `has_role()` is not a valid check for an arbitrary profile id), and the correction that `decided_by` has no consumer — the severity language attached to it was wrong.

Prior substantive update: **2026-07-29** — the seven-fold pass plus the completion marker. Folded: the creator onboarding flow and its shell, signup posture, the write-once timezone and its heal, Part II's rescoping to household-only, the non-creator first run and the completion-marker seam, Part II's v1 shape, and the onboarding-sequence change and PWA install door. Corrected: the `actor_label` derivation prescription, the dead "fourth activeness surface" note, the false `set_enabled_modules` claim, the COPPA shield, grant drift's severity, Part II's client-engine rationale, and the "empty board = top structural gap" line.

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
- **"Quest" is the universal object term.** Every task object is a quest, assigned or not. Open/Assigned **status badges** carry the state — the *word* never changes with it.
- **"Bounty" survives as a proper noun in exactly one place:** the Briefing's unclaimed-quest strip, **"Open Bounties."** Nowhere else.
- **The tier ramp is DIM → WARM → HOT → BLAZING.** Four tiers, derived from points, driving visual + animation intensity.
- **"Legendary" is NOT a fifth tier.** A **campaign-completion override state** — a one-off significance flag on the feed.
- **The mascot is PIP. LOCKED 2026-07-26.** Cinder and Holt are DECLINED: both fine names attached to a character that already has one.
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
- **The semantic reason matters more than the mechanical one:** an ember means *an adult approved this, and it buys me a real thing.*
- **The seal breaks quietly.** The requests that will arrive — *"can module currency buy a Vault reward?"* and *"can a kid trade it for embers?"* — are DECLINED in advance.

**2. MINTING REQUIRES AN APPROVAL-AUTHORIZED ACTOR. APPROVAL AUTHORITY IS ADULT-ONLY.** *(LOCKED 2026-07-25.)*
Embers come into existence only when an actor holding approval authority approves. That authority is adult-only, enforced at the trigger layer by `a_enforce_quest_update_authority`.
- **This is a rule about AUTHORITY, not about party count.** For a kid it necessarily means a second person. For an adult it is satisfied by themselves — adult self-completion writes `status='approved'` + `approved_by=self` atomically, and **the gate is satisfied, not bypassed.**
- **The residual hole is real and deliberately unclosed.** An adult can mint themselves unlimited embers. They would be buying "pick dinner" and "one hour alone" — household-internal favors with no external cost. An adult lying to themselves is not a problem this app should solve.
- **The kid case is where it bites, and it is why the fitness module cannot mint embers.** A kid's ember requires an adult to verify, and **nobody can verify a squat set.**
- **A sealed, in-app-only currency needs no approval gate at all.** Outside the adult gate on kids' embers, **Emberhold does not verify that people did what they said.** We are not the police.
- **⚠️ AUTHORITY AND ATTRIBUTION ARE TWO DIFFERENT QUESTIONS AND THIS RULE ONLY GOVERNS THE FIRST.** *"May this action happen"* is authority — session-scoped, gated on the acting session holding the parent role. *"Who did it"* is attribution — the specific adult who cleared the PIN. **Conflating them produced the redemption defect fixed 2026-07-30:** the authority gate was correct and the attribution was the session owner. **Any future gate work must state which of the two it is changing.**

**3. ONE TIMELINE.** The calendar is shared. A workout, a dentist appointment and a party are all events on the same surface. No module ships a second calendar.

**4. THE MONETIZATION SEAM IS HOUSEHOLD-LEVEL, ALWAYS.** Per-class, per-character, per-kid, or per-module-per-kid SKUs recreate a *"buy each kid their skin"* dynamic **inside a household tool** — the worst possible seam between a parent and their kids. One purchase, everyone gets everything. **No exceptions, ever.**

**5. ONE MEMBER TABLE. A MODULE NEVER CREATES A PERSON.** *(LOCKED 2026-07-25.)*
Exactly one member-creation path exists. Modules **read** hearthmates from the existing member table, render them with the **existing hero-portrait component**, and point at the existing member identifier. An empty state routes to the add-hearthmate flow; it never offers a local shortcut.
- *Earned the hard way: a fitness prototype shipped a free-text "Who's training?" field that minted its own rows.*
- **Deliberately unsolved:** filtering a module's member list by "Adults" is a proxy for "participates," and it will break. When it breaks the answer is a **per-member flag**, not a role check. Not built now.
- **The rule pays a dividend nobody designed for.** Forge's display mode uses the existing roster component as its session lane.

**6. BRIGHTNESS = HEAT = IMPORTANCE.** The hierarchy principle, the animation-intensity dial, the avatar-luminosity floor, and the Vault's affordability-as-heat mode switch are one rule wearing four hats. Every module and register inherits it.

**7. FREE IS A FULL TOOL. PAID IS DELIGHT, *OR* MARGINAL COST.** *(Amended LOCKED 2026-07-25.)*
The membrane applied to money. Paid may never be access to basic function — **with one generalized carve-out**: a feature with a genuine, unavoidable **per-user marginal cost** may be paid.
- **GUARDRAIL, so the clause doesn't rot:** the cost must be *real and unavoidable, not architected into existence.* **Paywalling a rest timer would be indefensible.**
- **The named test case is SUSPENDED, not deleted.** *LLM-generated* programming was the clause's live example. **Forge is scoped to one household (2026-07-27), so nothing in it is currently paywalled.** The test case reactivates the day Forge meets strangers.
- Generalizing rather than granting an exception is deliberate.

---

## The hold (the tenant)
- **Members ("hearthmates"):** name, avatar (hero portrait), role (**Adult / Kid** — display label; role enum unchanged), class (**Forge / Garden / Keep / Hall** — the register).
- **Model:** profile-switching, shared-device first (Netflix-style — tap to switch), because young kids have no phone. Join-code is the *secondary* path.
- **Kids have no auth identity ON THE SHARED-DEVICE PATH** — sub-profiles acting under an adult's authenticated session. *(This is also the reason naive `auth.uid()`-based RLS fixes don't work — see Security.)*
- **⚠️ THERE ARE TWO KINDS OF ADULT PROFILE, AND THEY ARE NOT INTERCHANGEABLE.** *(Found 2026-07-30.)*
  - A **join-code adult** signs up with their own email and password. `profiles.id = auth_user_id` — they have a real `auth.users` row and their own `auth.uid()`.
  - An adult minted by **`create_adult_profile`** has a `profiles.id` and **no `auth.users` row at all.** They exist only as a sub-profile under someone else's session, reached by profile-switch and PIN.
  - **THE CONSEQUENCE, AND IT IS SHARP: `has_role()` IS NOT A VALID CHECK FOR AN ARBITRARY PROFILE ID.** It reads `user_roles.user_id`, which only exists for auth-backed identities, so it returns **false for a legitimate, PIN-verified `create_adult_profile` adult.** **`profiles.role` + `profiles.status = 'active'` is the authoritative source for an arbitrary profile id**, and it is trigger-protected by `enforce_profile_role_change` (parents only). **`has_role(auth.uid(), 'parent')` remains correct for the acting session and only for the acting session.**
  - *This is why the Vault-page approval path is correct without a PIN and the wall path is not: on the Vault page a co-parent is signed into their own account, so `auth.uid()` names them. On the wall one device holds one session and serves both adults.*
- **Adult profiles are PIN-locked, fail closed.** A PIN is *mandatory* at adult creation (with confirm-entry); existing PIN-less adults are intercepted and forced to set one. No path into an Adult profile without a PIN. This gives the approval gate teeth (anti-cheat).
- **⚠️ THE COPPA SHIELD DOES NOT COVER THE OTHER DOOR.** A minor who joins **by code** has an email, a password, and a real auth row. A 13-year-old with their own phone and a join code is **normal traffic, not an edge case** — the join form has an adult/kid selector they will use. At 13 it is fine and at 10 it is not, and **nothing in the path asks.** Gate C item.
- **Adult profile creation** routes through the `create_adult_profile` SECURITY DEFINER RPC.
- **PIN / password recovery (three locks):** (a) **adult-resets-adult** — a reset (↺) on each other adult's member card clears their PIN. (b) **account-owner master path** — "Forgot PIN?" clears that profile's PIN via the owner-gated `set_profile_pin` RPC. (c) **account login** — "Forgot password?" → Supabase built-in reset → `/reset-password`. *(Threat note: an acting adult can reset another adult's PIN — peer trust, fine for a 2-adult hold; revisit for peer/roommate holds.)*
- **A joining member's PIN is set AFTER an existing parent admits them, never during signup.** `complete_household_setup` always creates a joining member as kid/pending regardless of requested role. **The join path renders no PIN field at all.**
- **Hold membership:** an owner; an editable hold name. **Invite to the Hold** fires the Web Share API with a prefilled message + deep link `theemberhold.com/join?code=XXXX`. **Join code visibility + invite are Adult-only.**
- The **"Whole hold" entity is marked by the keep glyph** in household-amber.
- **A hold has a timezone.** `families.timezone` (IANA, NOT NULL). See *The household clock*.

## Registers and modules
**Registers are aesthetic. Modules are functional. They are not the same axis and they never gate each other.**

**The four registers — Forge · Garden · Keep · Hall** — are an avatar class and an ambient theme. **A member's register has no relationship to what their hold has enabled.**

> **SUPERSEDED (2026-07-25, same day):** the reading that the four registers were four functional layers. It failed decomposition. **Keep** is quests. **Garden** is quests. **Hall** is lists, campaigns and calendar events. Only fitness failed to decompose, because it **cannot mint embers without breaking constitutional rule 2**. **Fitness is the odd one out, and it is the only module.**

**Module enablement is household-level, offered at the END of onboarding, and reversible by an adult** *(LOCKED 2026-07-25)*. Stored as `families.enabled_modules text[]`, written **only** through the `set_enabled_modules()` RPC.
- **It is a binary and it is a BRANCH, not a toggle.** Selecting Forge **launches a second setup flow** (equipment, experience, goals).
- **DESIGNED, NOT BUILT.** `/setup/intent` is unrouted, unlinked, never loaded by a human, and **nothing reads `enabled_modules`.** **Parked with a trigger — finalized when Forge is built.** Do not delete it.
- **The RPC is load-bearing and not a style choice.** `families.is_founder` is service-role-write-only. The RPC means **zero new grants on `families`.**
- **Never per-member** (constitutional rule 4). **Never offer a module that doesn't exist.** **Reversible** — disabling hides surfaces, never destroys data.
- **The column is correctly named as shipped and needs no migration.** `['household']` = base; `['household','fitness']` = base + Forge.
- **⚠️ CORRECTION — the `'training'` → `'fitness'` string change is NOT free.** `set_enabled_modules()`'s allowed set still reads `'training'`, and any existing row carrying it must be migrated in the same change. **A data migration, not a string edit.**

---

## Architecture (the bones)
- **Everything is an "entry."** Shapes: quest, calendar event, list, list item, meal. Only **quests** carry the platform game.
- **The backbones:** the **calendar** (one timeline, constitutional) and the **ember economy**.
- **Three horizons:** Engine (daily quests), Campaigns (seasonal collective goals), Archive (the forever adventure log).
- **One spine for movement:** the **activity feed** (`activity_log`) — an append-only stream every meaningful state change writes to.
- **The `+` is the universal capture.** Inside a create flow it transforms into that form's submit action. On a single-purpose surface that owns its own create action, the `+` FAB is suppressed entirely.
- **Personal vs shared views are filters, not separate data.**
- **Role-aware home.** Adults land on **the Briefing**, Kids land on the **game board**. Same nav slot, different surface.
- **Role-aware depth is filters, never separate systems** — the non-creator first run is the strongest proof of it yet.
- **THE MEMBRANE.** The platform game lives in **quests only**. Calendar, Lists, meals and notes are clean utilities that may *optionally* spawn a quest. Lists writes nothing to the activity_log spine.
  - **Module amendment** *(LOCKED 2026-07-25)*: **a module may carry at most ONE game of its own, sealed from the platform's.** **No module currently exercises this.**
  - **Naming a utility is not touring it.** First-run may show Lists, Calendar and Campaigns by name; it may never walk a user into one.

## The audience pattern (a first-class architectural primitive)
**A nullable/defaulted `audience` enum on an object + a viewer-role WHERE clause on its read query.** No second screen, no duplicated component, no parallel table.

- **`quest.audience`** — `anyone` / `adults_only` / `kids_only` (default `anyone`). Scopes an *unassigned/open* quest.
- **`reward.audience`** — `household` / `adults_only` (default `household`). Powers the couples rail.
- **The rule:** membrane-safe. Audience changes **visibility only** — never the sink, never the flow, never eligibility to earn.

Any future "X should only be seen by Y" want gets answered with this pattern before anyone proposes a new surface.

---

## The household clock (LOCKED 2026-07-23; the heal DRAFT 2026-07-27)
**The canonical clock for any household-local date is HOUSEHOLD-local, derived server-side from a single named function.**

- **The mechanism:** `families.timezone` (IANA, NOT NULL) + **`household_today(fam uuid)`** (STABLE).
- **`CURRENT_DATE` is banned in any expression touching `due_date`.**
- **Why not client-stamped:** `handle_quest_approval()` is a BEFORE-UPDATE trigger with no caller and no place to accept a client-supplied date.
- **This is a class, not an instance.** Reported at one site; enumeration found **six** live violations across two functions.
- **Capture SHIPPED 2026-07-26.** `complete_household_setup` validates the browser's resolved timezone against `pg_timezone_names`, falling back to `America/Los_Angeles`.
- **⚠️ THE COLUMN IS WRITE-ONCE, AND THAT IS A DEFECT, NOT A DESIGN.** Thirteen pre-existing holds were backfilled with the Pacific default; any hold created while its owner is travelling is permanently wrong with no recourse.
  - *Reads as a bug and isn't: `profile.tsx` calls the same RPC. It cannot overwrite — precisely because there is no update path.*
- **The remedy (DRAFT — contingent on the build landing and on glass verification):** `families.timezone_confirmed_at`, plus a parent-gated SECURITY DEFINER `set_household_timezone()`. A **set-once silent heal** fires only while `confirmed_at IS NULL`. **No table-level UPDATE grant on `families` is ever added for this.**
- **THE RULE THAT GENERALIZES: a household's timezone is where the household LIVES.** Moving is a decision and travel is a trip, and no device signal can distinguish them.
- **Rejected, recorded so they stay rejected:** wiping the column; a UTC offset instead of an IANA name; NTP; per-member timezones (**a chore is done at a house, not at a person**); a settings form alone.

## The activeness model (LOCKED 2026-07-21)
**`isActiveQuest(quest, today)` in `src/lib/quest-helpers.ts` is the single shared predicate.** Every surface that asks "is this quest live right now" imports it. None defines its own.

- **This exists because three surfaces each defined "active" independently** and drifted apart — a defect *pattern*, not a bug.
- **Any new activeness surface imports the predicate or is wrong by construction.**
- **The pattern has recurred at least once since, in a different column.** Two derivations of *role* now exist — `FirstRunGate` reads `profiles.role` while `useMyProfile()` derives from `user_roles`. **⚠️ The 07-30 finding makes this worse than a style divergence: the two sources genuinely disagree for `create_adult_profile` adults, and only `profiles.role` is correct for an arbitrary profile id.**
- *(The suspected "fourth activeness surface" on the member profile / roster was killed by data on 2026-07-26.)*

---

## Data model — the Quest object
- `title` — short text
- `points` — integer *(internal field name; user-facing term is **embers**)*. **Minimum 1 — zero-ember quests are illegal.**
- `created_by` — member who posted it
- `assigned_to` — empty = open (anyone claims); set = directed
- `audience` — **anyone / adults_only / kids_only** (default anyone).
- `campaign` — empty = everyday; set = tagged
- `status` — open → claimed → submitted → approved (directed quests start at claimed)
- `approved_by` — the actor holding approval authority; **embers only count once set**
- `due_date` — `DATE NOT NULL`. **Backend-only — never user-facing.** The board and every open-bounty surface filter `due_date <= household_today()`. **This gate is load-bearing.** *(The column still carries `DEFAULT CURRENT_DATE` deliberately.)*
- `recurrence` — none / daily / weekly / **monthly**
- `rating` — optional 1–5, quality signal only, does NOT change embers
- `is_favorite` — star → appears in a quick-add template list
- `tier` — derived from points: **DIM → WARM → HOT → BLAZING**
- Timestamps: created, claimed, submitted, approved

### The recurring lifecycle (LOCKED)
- **Fixed calendar anchors.** Weekly → the **Monday** of the following week; monthly → the **1st** of the following month; daily → tomorrow. There is **no day-of-month selector**. Anything bespoke is a **calendar event**, not a recurring quest (the membrane).
- **The anchor arithmetic DISCARDS the user's chosen day, by design — and the UI owes them that fact.** **The recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.**
  - *Confirmed against live data: all four monthly quests produced exactly one successor each, all dated 2026-08-01. The suspected clamp-and-drift failure is **structurally impossible here** because the day-of-month never enters the arithmetic.*
- **Spawn on approval only. There is no timer.** One shared spawn path: `handle_quest_approval`.
- **The successor anchors to the household's today, not to the completed instance's old date.**
- **Per-cadence rollover is asymmetric — and only the daily half is finished.** Daily **rolls forward on miss**: `roll_missed_dailies` archives the stale instance and spawns a fresh one for today. Weekly and monthly do **not** roll.
- **⚠️ CORRECTION (2026-07-26): an unapproved weekly or monthly sitting past-due forever is a DEFECT, not correct behavior.** One instance lingering is right — it is still owed. **Unbounded accumulation across cycles is a guilt pile.** Two are stranded on the live board. **The roll-forward rule is NOT YET DECIDED — this needs a design pass before a migration is writable.** The STALE chip predicate is likely the same fix.
- **`roll_missed_dailies` fires on every board mount, unconditionally.** This is why its comparison must use the household clock.
- **Rollover has no health indicator.** A stuck daily and a healthy one are indistinguishable from the glass. Logged as an observability gap.

**Adult self-completion:** when the submitter is an Adult, completion writes `status='approved'` + `approved_by=self` **atomically**. **The gate is *satisfied*, not bypassed** (constitutional rule 2).

**Two load-bearing triggers — do not drop or refactor without replacing the guarantee:**
- **`a_enforce_quest_update_authority`** — BEFORE-UPDATE, fires before the approval handler, restricts approval to adults. **The lock on the ember economy and the enforcement point for constitutional rule 2.** It gates on the **session owner** holding `has_role parent`, and separately forbids non-parents from changing `approved_by`.
- **`enforce_quest_family_refs`** — rejects any quest whose `claimed_by` / `assigned_to` / `approved_by` points outside the quest's own family. The cross-tenant guard.

**⚠️ `quests.approved_by` IS VALIDATED MORE WEAKLY THAN `redemptions.decided_by` NOW IS.** *(Established 2026-07-30 by a recon briefed to disprove — this doc previously held quest approval up as the correct model, and that was wrong.)*
- The combined guarantee is only: *"the acting session is a parent, and the value written names some profile in this household."*
- **Neither trigger checks that `approved_by` names an ADULT at all.** A client can write a kid's profile id into it and both triggers pass. Neither checks it matches the PIN-verified identity from that request.
- **Tolerable, not correct.** Under the walk-up trust boundary this is intra-household, and no cross-tenant path exists. It is a sweep target, not an incident.
- **The pattern to sweep toward is `approve_redemption`'s** — a validated optional actor id, checked for same-family AND active-adult, raising rather than falling back.

**Quest creation is UNGATED, and that is a decision, not an oversight** *(LOCKED 2026-07-26)*. **Approval is the real gate and it holds:** a kid can propose a 500-ember quest and cannot cash it.
- **What changes is the CURRICULUM, not the permission.** **Absence, not a locked door.**
- *Known inconsistency, logged and harmless: `campaign.$id.tsx` wraps its create affordances in `{isParent && …}` while the global FAB and QuickAddTray do not.*

**Planned extension (parked):** `objectives[]` — an array of checkable steps. Steps don't mint; only turn-in does.

---

## Onboarding (Pip-guided)
Pip, the ember-sprite mascot, is the app's guide. Pip is **guidance only** — read-only, never awards embers, unlocks, or gates anything.

**First-run is contextual setup, not a feature tour.** It is guided *doing*: the household performs the loop with its own names in it, so the pitch is **true** rather than claimed.

### The shell
Every first-run flow renders inside one **`SetupShell`** — a step registry plus chrome. **The shell is neutral machinery:** it references no role, no creation state, no session state.

- **One-directional.** Skip is a single affordance **owned by the shell**. Skip always exits to the board and never advances. **No back navigation.**
- **Each screen writes on completion, never batched at the end.** *(Verified on the glass.)*
- **Rejected:** an overlay tour with coach marks and spotlight state.

### The creator sequence (SHIPPED)
`add-family → first-quest → stock-vault → the-hold → recap → add-to-home → board`

1. **add-family** — hearthmate repeater: name / avatar / role; Adult requires PIN. **Header speaks the task.**
2. **first-quest — the activation doorway. BUILT.** Pre-filled chips, "Light the quest." **The first quest may be assigned to a named hearthmate OR posted open to anyone** — assignment is visually primary, open-to-anyone is a full-width peer option, never a downgrade.
3. **stock-vault** — the sink half of the loop.
4. **the-hold** — Lists, Calendar and Campaigns: **named and shown, never explained, never entered.** Its only job is to stop the mental model from setting as "chore app." **This is a deliberate deviation from "setup, not tour," and it is reasoned — do not "finish the cleanup" by cutting it.** Ships with **labelled empty frames until real screenshots exist.**
5. **recap** — "Welcome to Emberhold" (first run) / "welcome back" (replay only). Carries the flow's **terminal navigation**.
6. **add-to-home** — the install tutorial.

**Order is load-bearing.** The final thing a user reads has to be the thing they do next.

**KILLED 2026-07-29 — the second loop recap.** **The loop card belongs to the landing page and on-demand help, not to first-run.** *(Mechanical warning: `handoff` carried the terminal navigation. **Name every consumer of a route before removing it.**)*

**The historical gap is CLOSED.** "A new hold lands on an empty board with no path to a first quest" **is built.**

### Installation (the PWA door)
**Install and standalone launch are delivered by the manifest and the iOS meta tags. NOT by a service worker.**
- **Manifest:** `name`, `short_name`, `description`, `start_url: /`, `display: standalone`, portrait, 192 + 512 icons, `background_color: #1A110B`.
- **iOS:** `apple-mobile-web-app-capable=yes`, `apple-touch-icon` → `/icon-180.png`, `apple-mobile-web-app-title=Emberhold`, `theme-color=#1A110B`, `apple-mobile-web-app-status-bar-style=black-translucent`.
- **The tutorial is ONE screen** riding the walkthrough that already exists.
- **Its asset is a real captured share-sheet screenshot**, framed in a charred forge-window ember border.
- **Platform-conditional copy**, a **webview-escape line**, and a **suppress-if-installed** check.
- **The service worker is a separate job and belongs to Gate B.** **It earns its own careful pass.**

### The non-creator sequence (BUILT, DARK)
**The same `SetupShell` with a second step registry.**

- **Why not re-route the creator screens:** of the three that write, **two are parent-gated.**
- **THE PROPERTY THAT MAKES ONE BUILD SERVE BOTH:** a joiner sets nothing up, so every step is *teaching* rather than *doing*, and **a flow that writes nothing has no permission surface to differ on.**
- **Two arrays, split by role.** Adult: the board, approving, the Vault as operator, what the PIN is for, quest authoring. Kid: the board, claim/complete/submit, embers. **Authoring is ABSENT from the kid array.**
- **The flow EXPLAINS the adult PIN and never collects it.**
- **Both non-creator doors are in scope** — join-by-code and profile-switch.
- **The role selector on the join form cannot corrupt the flow, by construction.**
- **DECLINED: telling a joiner what role they were confirmed as.** **The demotion is a household conversation, not a system message.**

### The completion marker (the flow's one write)
**A per-profile DATABASE COLUMN.** Not derived, not client storage.
- **The trigger is a STATE CHECK on arrival in the authenticated layout.**
- **A derived gate CANNOT REMEMBER A SKIP.**
- **`localStorage`/`sessionStorage` are rejected outright.**
- **Seam:** one read and one write function in `src/lib/first-run.ts`.
- **⚠️ THE TRIPWIRE, AND IT IS NOT OPTIONAL.** `FIRST_RUN_MARKER_AVAILABLE`, `hasCompletedFirstRun` and `markFirstRunComplete` go live in a **single change.**
- **SHIPPED 2026-07-29.** `profiles.first_run_completed_at`, backfilled in the same migration. Written through a SECURITY DEFINER `mark_first_run_complete()`. **Verified end to end on the glass.**
- **⚠️ THE MARKER'S READ AND WRITE ARE BOTH ACCOUNT-HOLDER-SCOPED, AND THEY MUST BE FIXED AS ONE CHANGE.** `FirstRunGate` reads via `getActiveMemberIdSync() ?? auth.uid()`; `mark_first_run_complete()` writes `WHERE id = auth.uid()` and `markFirstRunComplete` contains a literal `void profileId;`. A kid sub-profile's row id is **not** any `auth.uid()`, so a kid's write updates **zero rows and returns success**.
  - **The two bugs currently cancel, which is the whole danger.** **Fix the read alone and you get the hostage loop.**
  - **The fix is a validated `profile_id` parameter, family-checked server-side.**
  - **This is NOT the `isActiveQuest` divergence pattern.** **Divergence announces itself; cancellation hides.**
  - **⚠️ CONFIRMED INDEPENDENT of the redemption fix (2026-07-30).** The marker's identity comes from `getActiveMemberIdSync()`; redemption's comes from `verify_profile_pin` against the adults list. **No shared helper, hook, or RPC.** They are two instances of one habit, not one coupled defect.
- **The arrival gate must exempt EVERY walkthrough tree, not just its own.** The exemption now names both `/first-run/` and `/onboarding/` literally.
  - **A hold creator IS a first-run-gate candidate.**
  - **Residual, accepted:** a creator who bails mid-onboarding and returns to `/board` gets the joiner tour rather than resuming setup.

### Replay, and what does NOT fold into first run
**DECLINED — "Replay the intro" does not fold into the first-run flow.**
- **THE SEAM IS MODALITY.** First-run is a full-screen routed takeover; replay is a bottom sheet.
- **The conflation was content with container.**
- **The URL-reachability concern was never this item** — a routing guard, closed by the completion marker.

### The Forge branch (DESIGNED, PARKED)
When Forge is built, the module question is **one binary at the END of hold setup**, and selecting it launches a second setup flow. **The activation doorway stays last regardless.** **None of this is built.**

**Feature tour is on-demand.** The loop card (**"Post the work → Claim & complete → Approve & reward → Chase the campaign"**) doubles as the marketing pitch.

### Signup (LOCKED 2026-07-26)
**Signup collects EMAIL AND PASSWORD ONLY before email verification.** Everything else is collected **after** verification at finish-setup. The pre-verification `localStorage` stash is **deleted**.
- **The mechanism was storage partitioning, not broken code.** Tapping the confirmation link opens the mail client's in-app webview, a *different* partition.
- **Accepted cost:** users who confirm in the same browser lose a no-retyping shortcut.
- **Two items closed as side effects:** the plaintext adult PIN in `localStorage` (**closed by deletion**), and cross-device signup as a special case.

---

## The economy
- **Sources:** quests mint embers, on approval by an adult. **Sinks:** the **Vault**. **Pools:** campaign collective bars.
- **Earned vs. spendable split.** Ranks tracks **lifetime embers earned**. The Vault draws from a separate **spendable balance** (earned − redeemed). **Spending never costs rank.**
- **Two reward rails:** the *free* rail (spectacle, status, the win) and the *costed* rail (the Vault).
- **Individual redemption is the model.** Pooled/shared earning is what Campaigns' EARNING type is for.

### The reward tiers
Rewards sit on the same **DIM / WARM / HOT / BLAZING** ramp as quests. One ramp, both directions of the economy.

### The adult reward menu (v1 — LOCKED, 7 items)

| Tier | Reward |
|---|---|
| **Dim** | Coffee / breakfast delivered to you |
| **Dim** | Pick dinner |
| **Warm** | Skip one chore-quest |
| **Warm** | Pick movie / show night |
| **Hot** | One guaranteed hour alone |
| **Blazing** | Solo morning routine |
| **Blazing** | Solo bedtime |

**Deliberately excluded — dishes.** The same chore cannot be both a source and a sink.
**Deliberately relocated — date night.** Moved into Campaigns as the EARNING type's first real content.

---

## Platform surfaces

### The Engine
Quest board, ember economy, recurrence, ratings, favorites, adult approval, Ranks leaderboard, Quest Log.
- Board is **date-windowed** (`due_date <= household_today()`) and reads activeness through the shared predicate.
- **Scope badges (adult view only):** `adults_only` → "Adults only"; `kids_only` → "Kids only"; `anyone` → no badge.
- **Claim-eligibility copy states itself exactly once per card.**
- **The Quest Log applies no `due_date` filter.** Currently load-bearing as a diagnostic surface. Whether that stays is an open deliberate call.

### The Vault (the reward store — dual-mode, both roles)
The store **switches mode on a binary affordability trigger.** Affordability-as-heat.

**Kid view:**
- **ZERO affordable → CATALOG ("The Wish Menu").** The full ladder by tier, cheapest-first.
- **ONE+ affordable → CURATED.** In-reach glowing set + live **Redeem** + "N embers left after" + next-unlock taunt(s).
- **Favorites are additive, never an override.**
- **Popularity / highest-redeemed is explicitly rejected as a curation signal.**

**Adult view — three zones:**
1. **Awaiting Your Approval** — pending *kid* redemption requests. Each card shows **redeemer name + face** (the kid-impersonation mitigation).
2. **Your Store** — the adult's own balance-anchored store. Adults redeem instantly, via `parent_self_redeem` — **a separate INSERT path that never calls `approve_redemption`.**
3. **Reward Menu · Manage** — edit/delete/add rewards.

**✅ REDEMPTION APPROVAL RECORDS THE PIN-VERIFIED ADULT. SHIPPED 2026-07-30.**
`approve_redemption` and `deny_redemption` take `(_redemption_id uuid, _approver_id uuid DEFAULT NULL)`.
- **NULL → `decided_by = auth.uid()`.** Correct for the Vault page, where each co-parent is signed into their own account.
- **NOT NULL → validated server-side:** same family, and an **active adult by `profiles.role`** (not `has_role()` — see *The hold*). **Raises on failure; never falls back to `auth.uid()`,** because a silent fallback makes a rejected approver look like a successful one.
- **The authority gate did not move.** The acting session must still hold the parent role. The parameter refines **attribution only** (constitutional rule 2's authority/attribution note).
- **The defect was WALL-ONLY.** The kiosk is the only surface where one session serves two adults. `wall.tsx` was already computing the correct PIN-verified `profiles.id` and discarding it in the mutation's destructuring one hop before the RPC.
- **⚠️ THE SEVERITY LANGUAGE PREVIOUSLY IN THIS DOC WAS WRONG, AND IT IS RECORDED HERE SO IT ISN'T RE-DERIVED.** This section carried a breached-deadline flag and a first-prompt-of-the-next-window priority. **`decided_by` has zero consumers** — it is fetched by `select("*")` and rendered by nothing. **The fix was worth shipping because it cost about one credit and zero taps, not because it was urgent.**
- **The copy on `/first-run/adult/pin` is now true.** It was written by someone who did not know the defect existed, and it is what settled the fork. **Fixing the string instead was DECLINED and stays declined.**
- **The prescription this doc used to carry — "quest approval's behavior is the correct one and redemption moves to match it" — is SUPERSEDED.** Quest approval is the weaker path. See *Two load-bearing triggers*.

**Reporting deliberately does NOT live here.** Redemption history and reward-performance belong on a future admin surface.

**The couples rail (`reward.audience`).** `household` / `adults_only`, default `household`. Membrane-safe.

### Campaigns
Shared-goal containers a quest tags into. Two types via `goal_type`: **PLANNING** and **EARNING** (pooled embers — **the home for shared/couple goals**). Progress renders as a bare **%**. Completion fires the **Legendary** override state.

### Calendar
Standalone event CRUD: month grid, per-member color dots, today-glow, day-tap list, event detail. **Native OS date picker.** Event **creation** is a feed write point; edits aren't. **One timeline is constitutional.**

### Lists
A generic shared-list primitive (`list` / `list_item` / `list_section`). Index + detail (docked add-item bar, **strike-and-sink**, user-defined **sections**, per-item delete, "Clear checked"). **Deleting a whole list is adult-only.** **Off the activity_log spine** (membrane).
- **Add-item bar = full-width solid docked footer.** The global FAB is suppressed on list-detail.
- **Search bar** appears past ~50 items; **searches checked items too**.

### The Briefing
The Adult command center and the **Hub's seed**. Zones, priority-ordered by brightness=heat=importance: **NEEDS YOU** · **OPEN BOUNTIES** · **FEED** (48h) · **THE ROSTER** · **ON THE HORIZON** · **CAMPAIGNS**.

### In-app notifications
Global header bell. Badge = actionable items for the current profile only. **Stateless.**

### The wall (display / kiosk mode)
Always-on, no-login, full-screen on a cheap tablet. Top full-width FEED ticker; bottom-left ~3/4 calendar WEEK view; bottom-right ~1/4 avatar-collapsible ACTION RAIL.
- **THE WALL SHOWS AND PROPOSES. IT NEVER MINTS, SPENDS, APPROVES, OR EDITS.** Claim/complete move status only; approval stays PIN-gated. On-behalf writes (`wall_request_redemption`) *request*; they never commit — that function never touches `decided_by`.
- **The rule protects the EMBER ECONOMY, and only that.** It does **not** bind a Forge screen.
- **⚠️ THE WALL IS THE ONLY SURFACE WHERE ONE SESSION SERVES TWO ADULTS**, which is what makes PIN-derived attribution meaningful there and unnecessary on personal-device surfaces. Any future "who did this" column should be reasoned about from this fact first.
- **The only semi-public Emberhold surface a non-customer can encounter** — which is why wall-visibility ranks cosmetic catalog items.

---

## Navigation
- Bottom tab bar, **7 tabs**: BOARD · CAMPS · CAL · **LISTS** · VAULT · RANKS · YOU.
- The **"+" FAB is raised** to a kiss-overlap above the bar.
- **Open question, currently DEFERRED:** seven tabs is already one past mobile comfort. Forge's contained-route posture defers this entirely.

---

## Avatars (the hero roster)
- **Hero portraits are the sole avatar system.** Never a bare photo in the circle.
- The picker is grouped by the four registers and **collapses per register** to ~4 portraits.

### The four registers
- **FORGE** — warriors, protectors, ember-forged heroes
- **GARDEN** — nature-touched growers and wildsfolk
- **KEEP** — steadfast watchers and guardians
- **HALL** — bards, storytellers, dancers, revelers, hearth-tenders, hosts. **Food is ≤ ~1/3 of the register.**
  - *Renamed from **FEAST** (7/03) — a redefinition, not a label swap. If anything named Hall ever reads as a food surface, it has re-narrowed to Feast and it is wrong.*

### The roster
**48 characters, 12 per register.**
- **Register spread (LOCKED):** every register must span **cute / majestic / COOL**, carry **2–3 genuine kid-magnets** *and* a cool/battle-ready character.

### The Avatar Style Spec (LOCKED — the band)
**The floor is a floor, not a ceiling.**

1. **LUMINOSITY** *(most important)* — the subject glows from within / carries strong warm ember rim-lighting.
2. **STYLIZED ILLUSTRATION** — clearly hand-drawn painterly fantasy art.
3. **SATURATED JEWEL COLOR** — rich, clean, saturated. Never muddy brown earthy naturalism.

**Composition rule (LOCKED): circular-crop-safe framing.**
**Background rule:** derived from THAT character, dark enough to blend into `#1A110B`. **No uniform gold frame.**
**Diversity rule:** name the archetype traps as **"at most once in the entire roster."**
**Asset hygiene:** image gen always lies about "transparent background." Sidestep with filled-dark backgrounds.

**The empty seat.** A hearthmate added without an avatar renders as a silhouette in a frame. **In the picker and on the roster that is the honest state and is not auto-filled.** What is owed is a **nudge**. **The wall is the one exception** — the wall carries a render fallback. *(Open decision.)*

### How to actually generate avatars
**The method lives in `playbooks/avatar-generation.md`.** Read it before touching a generator.

---

## Monetization (the Founding Guildhall)
- **One v1 SKU: the "Founding Guildhall."** A **one-time, household-level** purchase. Stripe Checkout + Supabase webhook + `families.is_founder` (**service-role-write-only**). Staying outside app-store rails retains **~97%**.
- **What it unlocks:** the paid share of the avatar roster. Free keeps **16**; the Guildhall opens the remaining **32**. The gate is a DB value (`system_flags.founder_gate_enabled`).
- **The free tier must be genuinely COMPLETE, not a demo.**
- **Household-level, always** (constitutional rule 4). **Membrane-safe cosmetics.**
- **The catalog is retention leverage, not acquisition insurance.** **Wall-visibility ranks catalog items.**
- **Next catalog item:** living-hold ambient themes, one per register, canvas particle-based. **Keep first.**
- **A sharper test than "delight vs. function": is this thing a CHOICE or a MEMORY?**

---

## Design system
- **Ember-lit aesthetic.** Base `#1A110B`, elevated card `#241813`, top-edge sheen `#6E4A2E`, primary text `#F2E3CB`, muted `#A07B54`. Ember ramp: `#BA7517` → `#EF9F27` → `#F8C13C` → `#FCDE5A`; gold rim `#E0A94A`.
- **Brightness = heat = importance** (constitutional rule 6).
- **Member identity colors are a SEPARATE system from the ember ramp.** Amber/gold `#E0A94A`, violet `#9B6BD6`, jade `#3FB37A`, steel-blue `#4A9FD6` (spare: rose `#E0607A`, teal `#2DB3A6`, coral `#E08750`).
- **The household keep glyph.** Tintable SVG, household-amber, ~20–22px inline.
- **Date pickers.** Calendar event dates use the **native OS picker**. Quests carry no user-facing date input.
- **Numeric inputs must be clearable.**
- **Depth, not flat:** layered surfaces, a 1px warm top-edge highlight, soft shadow beneath cards, glow on heat.
- **The FAB-as-submit / FAB-suppression pattern.** **Exactly one create control in the bottom thumb zone, always.**
- **Layered docked surfaces:** full-width **opaque** elevated layer with a hard top edge. Translucent floating capsules over scrolling content are banned.
- **A foreign asset gets a frame, not a redraw.**
- **Brand: two marks, one keep.** App icon; login crest. Motto **"by hearth & hold."** Plus **Pip**.
- **A register's palette may never override the ember-lit base or the brightness rule.**

## Experience layer (the juice)
- Completing a quest is theater. **Tier = intensity, register = theme — no new data model.**
- **Reactive micro-motion:** ember burst by tier, approval echo, new-approval pulse, count roll-ups, press feedback, breathing submit button.
- **AI-generated, never stale:** grow a library of themed vignette assets in the background, cache, select instantly at the win. *(Generate offline, select locally, instant on the glass, zero marginal cost.)*

## The activity feed (the event-log spine)
A single **append-only, permanent, immutable** stream.
- **`activity_log` table.** Fields: `id`, `actor_id` (soft, nullable), `actor_label` (frozen), `verb` (enum), `object_id`, `object_label` (frozen at write), `ember_delta` (nullable), `campaign_id` + `campaign_label`, `significance`, `created_at` (indexed).
- **`logActivity()` helper** — the single app-level write path. No DB triggers.
- **Five curated write points:** `quest_approved` · `bounty_posted` · `reward_redeemed` · `campaign_completed` · `event_created`.
- **CAP THE READ, NEVER THE WRITE.** **No pruning queue, ever.**
- **The identity fields are already server-derived.** `actor_id` derives from `auth.uid()`; `family_id` from `current_family_id()`; `ember_delta` is nulled for non-parents. **Only `actor_label` is client-supplied.**
- **⚠️ DO NOT DERIVE `actor_label` FROM `auth.uid()`. IT IS A REGRESSION, NOT A FIX.** Four of six `logActivity()` call sites **deliberately** pass someone other than the caller.
- **The column is doing two jobs** — sometimes "who clicked," sometimes "who gets credit." **The likely shape is a validated `subject_profile_id` with the label derived server-side from it: a third field, not a substitution.** *(2026-07-30: `approve_redemption`'s validated-optional-actor-id is now a working precedent for exactly this shape. Reuse it.)*
- **The feed is where an optional module touches the platform.**
- **Downstream consumers:** the notification RECENT panel · the full in-app feed view · wall/kiosk callouts · the **weekly recap** · the Adventure Log · the family message board.

---

## Security posture (design truth, not just a bug list)
- **The core rule: embers mint only on approval by an adult** — constitutional rule 2, enforced at the trigger layer.
- **⚠️ AUTHORITY ≠ ATTRIBUTION.** Session-scoped authority and actor identity are separate questions. **Any change to a "who did this" column must state which one it moves.**
- **⚠️ ROLE SOURCE IS NOT INTERCHANGEABLE.** `has_role(auth.uid(), …)` is correct for the **acting session** and only for it. **`profiles.role` + `status = 'active'` is the authoritative source for an ARBITRARY profile id**, because `create_adult_profile` adults have no `auth.users` row and `has_role()` false-negatives them. `profiles.role` is trigger-protected by `enforce_profile_role_change`.
- **The validated-optional-actor-id pattern (LOCKED 2026-07-30, from `approve_redemption`).** When a call site knows *who* better than the session does: an optional parameter, defaulting NULL to the session owner, validated server-side for same-family **and** the required role, **raising on failure rather than falling back.** A silent fallback makes a rejected actor indistinguishable from an accepted one. **This is the prescribed shape for the marker's `profile_id` and for `actor_label`'s `subject_profile_id`.**
- **The tenant boundary holds under authenticated cross-tenant attack (VERIFIED 2026-07-19).**
- **The table-grant surface is CLOSED (2026-07-26).** `anon` holds **zero** privileges on every table in `public`.
  - **The reframe that matters:** this was the Supabase default, with RLS as the intended gate. All fifteen RLS-enabled, exactly four anon-reachable policies, all RESTRICTIVE false denials.
  - **RLS gates SELECT/INSERT/UPDATE/DELETE and nothing else.** TRUNCATE, TRIGGER, REFERENCES and MAINTAIN were held by `anon` with nothing in front of them. **"RLS is the sole gate" was only ever half true.**
  - **Why the revoke was safe:** pre-login flows route through SECURITY DEFINER RPCs that execute as owner and ignore table grants entirely.
- **Load-bearing grant rules (LOCKED — do not regress):**
  1. **`member_spendable()` is family-scoped.**
  2. **Quest FK columns resolve to the quest's family** — `enforce_quest_family_refs`.
  3. **`families.is_founder` is service-role-write-only.**
  4. **`founder_gate_enabled()` / `my_household_is_founder()` execute is `authenticated`/`service_role` only.**
  5. **Module enablement writes only through its RPC.**
- **⚠️ A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.** `CREATE OR REPLACE` with an added `DEFAULT NULL` argument creates a **second overload**, and existing one-arg calls then fail as ambiguous. **Drop the old signature, recreate, and re-issue grants** — dropping drops the grants with it.
- **GRANT DRIFT IS DOWNGRADED FROM A RECURRING CLASS-LEVEL DEFECT TO A BOUNDED PLATFORM RESIDUAL** *(2026-07-26)*. The 07-26 migration altered default privileges for the `postgres` owner — the first fix aimed at the mechanism.
  - **What remains:** the `supabase_admin` default-privilege entry, unreachable from this connection.
  - **The standing rule:** **"re-read table grants after any Lovable platform-level change."**
  - **Convention when creating: grant `authenticated` first, then revoke `PUBLIC`. Order matters.**
- **Hand-applied DB changes are forbidden.** Structural changes verify against the **live** surface.
- **Schema is undone FORWARD** *(LOCKED 2026-07-25)*. Drop in FK order **without CASCADE**.
- **Join codes admit members to a household — gated by admit-on-approval.** **`current_family_id()` returns NULL for any non-active profile.** **Do not refactor `current_family_id()` without preserving the NULL-for-pending guarantee.**
- **The walk-up trust boundary (accepted, intra-household).** In the shared-session model a device-kid holds the owner's ambient parent JWT. **Intra-household, not cross-tenant.** **Three findings are ignored or downgraded *because of* this boundary.** The own-session-vs-per-member-auth fork is parked, and four open items wait on it.
- **Kid-vs-kid impersonation.** Kid PINs stay **off by default**. Mitigation already built: the **redeemer's name + face on the adult approval card**.
- **Analytics is a disclosure surface, not a bug.** `flock.js` **must be named truthfully in the Gate C privacy policy** — minors are genuine users.
- **Persistence debt:** Vault favorites use `localStorage`. Fine for validation, fatal for the shared-wall model.

---
---

# PART II — THE FORGE (the fitness module)

**Scope: the Draper household. Status: designed, not built.** Option A's shape is settled and written here as design truth. **Option B remains the only open fork.**

**Names:** stored value `fitness`; product name on screen **Forge**.

## Scope, and what it changes

**It is a module inside Emberhold, not a sibling app** *(LOCKED 2026-07-25)*. Merging deletes work: no second auth, no second tenant bootstrap, one member table, one PIN system, one avatar pipeline, one Founding Guildhall unlock.

**It is scoped to ONE HOUSEHOLD, not to strangers** *(LOCKED 2026-07-27)*. **Don't dilute the "wow" of Emberhold with a "meh" Forge.**
- **Rebuilding it standalone for two users is REJECTED.** **Standalone-for-two is a worse proportion than standalone-for-strangers.**
- **"Forge dilutes Emberhold" is structurally impossible.** **The real risk is SUBSTITUTION, not dilution.**
- **Money survives intact.** ~$192/yr of Fitbod cost avoidance counts identically to $192 of revenue.

**The bar is a USER TEST: Scott and May stop opening Fitbod.** *("A full Fitbod replacement" is RETIRED as the standard — read literally it has no exit condition.)*

**Why it is the only module.** Fitness **cannot mint embers without breaking constitutional rule 2**. **It does not mint, and it has no economy.**

**Containment posture:** a route at `/forge`, gated on `families.enabled_modules` containing `'fitness'`, **with no nav entry.**

**Explicitly out of scope:** client-facing training.

**Platform constraint, and it is hard.** **There is no HealthKit web API**, and the PWA posture is LOCKED. **Self-logged work is the only modality a PWA can honestly measure** — but *validation* is the only thing missing, not participation.

## v1 is a PRESCRIPTION ENGINE with a log attached

**Not a logger with programming bolted on.** Both users named *what exercise to do* and *what weight to use*. **Neither named logging.**

**Trust in the instruction IS the product.** **The reason string is the moat.** *"3×5 @ 185, avg RPE 6.7 — all sets complete under target, adding 10 lb."*

**The reason string stays COMPUTED, never narrated.** **A model may pick exercises and structure; it may not narrate math it didn't do.**

### The pre-session gate
**One screen, a few taps, before generation.** `progression.ts` autoregulates on RPE, a **backward** signal.

- **READINESS IS A SCALAR. INJURY IS A FILTER. Two controls, two lifespans, never one dial.** **A unified wellness/readiness score is REJECTED.**
- **An injury flag carries its own resolution, asked at flag time:** *reduced load* or *avoid entirely*. **Avoid-entirely is the expensive branch, because avoiding creates a hole something must fill — and that is what makes the catalog non-optional.**
- **Expiry rides the training split, not a timer.** **The split IS the timer.** The question is **three-way — still hurt / better / cleared.**
- **Inferring readiness from wearables is REJECTED.**
- **SCOPE GUARDRAIL:** one screen, three taps. **The moment it grows a body map, a pain scale, or a history graph, it has become a wellness tracker.**
- **The engine has no forward input path yet.** Scope this with rep-compensation.

### The engine
**Pure client-side TypeScript. Progression logic never lives in Postgres** *(LOCKED 2026-07-25)*. `src/lib/progression.ts` exists today: RPE-driven autoregulation, bounded subset-sum plate-snapping, Epley+RIR e1RM, 10/10 tests, zero Supabase imports.

- **⚠️ CORRECTION (2026-07-28): the rule does NOT rest on connectivity.** It survives on better grounds: the engine is already written and tested, and **even on perfect LTE a rest timer and a plate calculation over the network is worse than one running locally.**
- **Two real unlocks:** the exercise catalog can live in **Supabase rather than as a static asset**, and **live mid-session regeneration becomes viable.**
- **The connectivity clause is rescoped, not dead.**
- **Tests are mandatory here specifically** — a silent off-by-one corrupts training data that cannot be reconstructed.
- **⚠️ THE ENGINE HAS NO PROGRESSION AXIS EXCEPT LOAD.** **It cannot express *"175 instead of 185, so give me 8 instead of 5."*** It needs a **validity floor**.

### The exercise catalog — the single blocking dependency
**Movement pattern · muscle attribution · equipment requirement · substitution map.** **Everything good in the module is downstream of it.**

**Generated offline, reviewed by Scott, shipped as data.** **Make is a content FACTORY, never a runtime.**

**Core is programmed as real work, not accessory filler.**

### The session model
- **Contention resolution is a first-class feature, and no competitor could ship it.**
  - **The ladder: RESEQUENCE → SUBSTITUTE → MODIFY LOAD.** **Most collisions die at tier one for free.**
  - **Bias at generation, never schedule.**
  - **Equipment presence detection is CUT ON SIGHT.**
- **A time budget is a generation INPUT, not a schedule.** Its real job is **SIZING, not prediction.**
  - **Duration estimates calibrate from the user's own rest-timer data**, free.
- **NAG WITH THE REST TIMER; NEVER WITH SESSION DURATION.** **Interrupt only when there is an action attached.**
- **The rest timer is derived, never stored.** **No PIN on set logging.** **Attribution, not concurrency, is the real risk.**

### Data & units
- **Weight is stored in POUNDS.** **Kg is not a display toggle on lb data.**
- **Dumbbell load is logged PER HAND.** **This forces a `bilateral` flag on the exercise row** — **that flag cannot be added after data exists.**
- **A dumbbell e1RM must never be estimated from a barbell e1RM or vice versa.**
- **`prescribed` and `actual` are two fields, never one.**
- **Cardio is IN**, self-reported, and **prescribed rather than autoregulated.** **There is no reason string because there is no reasoning.**
  - **A session must hold ENTRIES OF TWO SHAPES and allow MIXED sessions from day one.**
  - **Bands are out of v1.** **Door unwelded, not opened.**
- **DECLINED for now: a session-RPE field for cardio progression.**

### Equipment
**Standards-plus-override.** Users select equipment **TYPES**; presets generate the load set. **No plate-by-plate inventory entry.**
- **Dropping inventory entirely is rejected.** **The inventory is not the mistake; making a user type it in is.**
- **THE TEST: if Scott's gym cannot be expressed as preset + overrides, the preset model is wrong.**
- **When the prescribed load isn't achievable, the user adjusts manually and the app compensates in REPS.**
- **⚠️ Equipment records need `exclusive` vs `shareable`.**
- **⚠️ The Smith machine offset is ASSUMED, not measured.** **A wrong offset corrupts every Smith e1RM forever.**

### Display mode (the garage screen)
**A separate always-on device that IDLES AS THE WALL and is taken over by a workout.** Not a toggle — **precedence.**
- **The device posture is genuinely different:** a Forge screen is **touched, wet-handed, between sets.**
- **THE AVATAR IS THE SESSION LANE.** **This is NOT split view** — it is the existing roster component doing double duty.
- **A free feature falls out of it:** contention becomes avoidable on sight.
- **It removes the iOS background-timer problem entirely.**
- **The wall's never-mints rule does NOT bind here.**
- **Two PWAs on one device via Fully Kiosk — do not build a launcher shell.**

### The LLM seam
**Generation happens at SESSION START, not per set.**
- **The API key lives in a Supabase edge function, never the client.**
- **The deterministic template path stays as the no-generation fallback.**
- **The real unlock of bespoke is the CONTEXT, not the model.**
- **Open:** whether the LLM plans both sessions in one call or one per person. **Contention effectively requires one call.**

### Participation
**"Adults only" is a proxy for "trains" and it will break.** **When it breaks the answer is a per-member flag, not a role check. Do not build it now.**

## The open fork — Option B

**Option A is v1 and is described above.**

**Option B — TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts. **No currency in the fungible sense — an artifact isn't *worth* anything, it's *evidence*.**

**What B owes before it could be written as truth:**
- Whether materials **pool** or are **claimed at commitment**.
- What materials are typed **on**.
- Whether a collection survives the calibration check.

**THE STANDING RISK.** **Option B does not get designed until Option A has been used for a month.**

## What does NOT come along at stranger scale
- **⚠️ PRESCRIBING AROUND A DECLARED INJURY IS A DIFFERENT ACT WHEN THE USER ISN'T YOUR WIFE.**
- **The equipment model is one garage with a known rack.**
- **The connectivity clause comes back.**
- **Forge display mode is a semi-public surface.**
- **The free/paid split reactivates** (constitutional rule 7).
- **Garage hardware is cold, dusty, and humid.**

---

## Tech & working model
- **Stack:** Lovable.dev (React + Supabase), mobile-first installable PWA. **Stripe Checkout + Supabase webhook** for the Guildhall. **Copilot Create** for avatar/asset generation. **Claude Code** for the codebase-reading jobs Lovable can't do from inside itself.
- **The lanes (LOCKED):**
  - **Scott** — vision, taste, final decisions. **Anything whose success criterion is visual.**
  - **jAIne** — hears, challenges, breaks down, translates intent into direction. **Cannot see.**
  - **Lovable** — engineers. Default lane for frontend and live-DB work.
  - **Claude Code** — text: code, config, strings, files, structure. **Syncs to `origin/main` before reading.**
- **SESSION LANE IS DECLARED AT OPEN.** **Credits and hands are INDEPENDENT switches.** **Design is always on.**
- **One writer at a time.**
- **Brief the floor and the intent; leave latitude on execution.** **jAIne's failure mode is over-specifying when under-trusting downstream.**
- **RIGHT-SIZE THE ARTIFACT TO THE TASK.** **The bloat instinct wears a thoroughness costume.**
- **Fetch the canon before producing anything.**
- **Recon before build. Brief recon to DISPROVE, not to confirm.** **⚠️ A PRESCRIPTION IN THIS DOC IS STILL A HYPOTHESIS.** On 2026-07-30 a disprove-briefed recon killed this spec's own "quest approval is the correct model" line. **Canon is the best current answer, not an exemption from testing.**
- **⚠️ ASK WHAT READS IT BEFORE BUILDING FOR IT.** A 🔴 whose column has no consumer is not a 🔴. **Severity in a doc outlives the evidence for it** — and the second instance was caught by Scott, not by recon.
- **Sweep the class before fixing the instance** — but **only when it is one.**
- **Decompose before you promote.**
- **UNBUNDLE WELDED ASSUMPTIONS.** · **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.**
- **CHECK THE DEPLOYED COMMIT BEFORE DIAGNOSING A MISSING FEATURE.**
- **RE-DERIVE A LOCKED RULE'S REASON BEFORE USING IT AS AN ARGUMENT.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.** **Fence the edit, not the read** — a recon needs to examine the surfaces it must not change.
- **A defensive layer is not verified by the migration that adds it.** **HTTP 200 is not "renders." A PREVIEW IS NOT PROD.**
- **"Shipped" means a completed end-to-end loop, verified on the glass — and WHERE THERE IS NO GLASS, VERIFIED ON THE LIVE DATA.** Some correct fixes render nothing. **Design-complete ≠ shipped, and an agent's report is neither.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **STATE LIVES IN THE REPO OR IT DOESN'T EXIST.**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit and P4×L8-class work, and the jAIne seat).**
- **The calibration check:** this is a family chore game with your kids' faces in it.

## The other docs
`north-star.md` (why + the gate ladder — read first, cold) · **this spec** (what it is) · `status.md` (where the build is) · `parking-lot.md` (what might be) · `decisions.md` (what we decided and why) · `playbooks/` (how to do a thing) · `archive/` (reasoning history). Full manifest: `canonical-manifest.md`.

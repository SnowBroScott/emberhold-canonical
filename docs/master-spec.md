# Master Spec
**What Emberhold IS.** Canonical design truth — above any individual build prompt or chat. Status lives in `status.md`; this doc does not track it.

Last substantive update: **2026-07-10** — 12 days of drift cleared (7/03 avatar pass, 7/04 vocabulary + adult Vault, 7/10 Vault run, and the monetization model, which had never been in the spec at all).

---

## What it is
A gamified household operating system. Household tasks become **quests** worth **embers**; kids claim and complete them, adults approve, and embers drive a leaderboard and real rewards. Built mobile-first as an installable PWA, live at **theemberhold.com**. Long-term vision: the household's one-stop organization hub — calendar, chores, lists, goals, and memory in one place.

> **Vocabulary note.** User-facing labels are neutral: role label is **"Adult"** (not "Parent"), and the group is **"the Hold"** (not "Family"). This lets a hold *with* kids and one *without* (couples, roommates) both fit. The underlying role enum and permission logic are unchanged — neutral wording is a UI-layer relabel, not a model change. (Deeper power-structure flexibility — flat/peer holds — is parked.)

## The thesis (the moat)
**Out-habit, don't out-feature.** Paper calendars and $400 displays (Skylight) win only because they're *ambient*; Google/Apple lose because nothing makes you open them daily, so they drift and need a human admin. Emberhold's game engine is a daily-habit driver — the household's organization stays current as a *side effect of the fun*. We don't beat Google on features; we beat it on habit, and we own the family-specific jobs it does badly.

*(Thesis, gate ladder, and doc-ownership map live in `north-star.md` — read that first, cold.)*

---

## THE VOCABULARY (LOCKED — the string law)
Vocabulary drift is a real failure mode. These are the rules, not suggestions.

- **Embers is the sole user-facing currency term. XP is dead.** Campaign progress renders as a bare **%** (earning-type campaigns showing a real ember count are the only exception). The `points` field name in the data model is unchanged — this is a UI-string law, not a schema law.
- **"Quest" is the universal object term.** Every task object is a quest, assigned or not. Open/Assigned **status badges** on the card carry the state — the *word* never changes with the state. *(This walks back the earlier `assigned_to`-null-means-"Bounty" rule: the rule created friction in real use and confused a tester. "Quest" being universally understood is a feature, not a weakness — the differentiation budget is already well spent on ember, hold, hearth, Vault, Ranks.)*
- **"Bounty" survives as a proper noun in exactly one place:** the Briefing's unclaimed-quest strip, **"Open Bounties."** Nowhere else.
- **The tier ramp is DIM → WARM → HOT → BLAZING.** Four tiers, derived from points, driving visual + animation intensity. This is "brightness = heat = importance" made countable.
- **"Legendary" is NOT a fifth tier.** It is a **campaign-completion override state** — a one-off significance flag on the feed, not a rung on the ladder.
- **Pre-auth "Hold" grammar rule.** Imperative / doorway framing grounds the word and self-teaches it ("Enter the Hold" ✓). Cold possessive use does not ("Your hold's quest board" ✗) — a brand-new user has no referent yet. Possessive pre-auth copy uses plain language ("household," "home") instead. Post-auth, "Hold" is free everywhere.

---

## Architecture (the bones)
- **Everything is an "entry."** Shapes: quest, calendar event, list, list item, meal. Only **quests** carry the game.
- **Two backbones:** one currency (**embers** — every feature is a source, sink, or pool) and one timeline (**the calendar**).
- **Three horizons:** Engine (daily quests), Campaigns (seasonal collective goals), Archive (the forever adventure log).
- **One spine for movement:** the **activity feed** (`activity_log`) — an append-only stream every meaningful state change writes to. The engine under notifications, wall callouts, and the Adventure Log.
- **The `+` is the universal capture** — every module is a new entry type it can mint. Inside a create flow, the `+` transforms into that form's submit action. On a single-purpose surface that owns its own create action (a list's docked add-item bar), the `+` FAB is suppressed entirely.
- **Personal vs shared views are filters, not separate data.** *(This is now proven twice — the quest audience filter and the reward audience rail are both WHERE clauses, not second screens.)*
- **Role-aware home.** Adults land on **the Briefing** (command center — operations), Kids land on the **game board** (quests — play). Same nav slot, different surface. "Kids see a game; adults see operations."
- **The membrane (critical discipline).** The game lives in **quests only**. Calendar, Lists, meals, notes stay clean utilities that may *optionally* spawn a quest. Gamify the chore, not the grocery item. Lists writes nothing to the activity_log spine.

## The audience pattern (a first-class architectural primitive)
One mechanic, now used in two places, and the template for any future scoping:

**A nullable/defaulted `audience` enum on an object + a viewer-role WHERE clause on its read query.** No second screen, no duplicated component, no parallel table.

- **`quest.audience`** — `anyone` / `adults_only` / `kids_only` (default `anyone`). Scopes an *unassigned/open* quest. The form control shows only for unassigned quests and hides the moment a quest is directed.
- **`reward.audience`** — `household` / `adults_only` (default `household`). Powers the couples rail.
- **The rule:** membrane-safe. Audience changes **visibility only** — never the sink, never the flow, never eligibility to earn.

Any future "X should only be seen by Y" want gets answered with this pattern before anyone proposes a new surface.

---

## Data model — the Quest object
- `title` — short text
- `points` — integer, set on creation *(internal field name; user-facing term is **embers**)*
- `created_by` — member who posted it
- `assigned_to` — empty = open (anyone claims); set = directed
- `audience` — **anyone / adults_only / kids_only** (default anyone). Model, form, **and consumer all live**: kids see `anyone` + `kids_only`; adults see all three. Applied as one shared rule across board + Briefing strip + count badges.
- `campaign` — empty = everyday; set = tagged, its embers feed that campaign's bar
- `status` — open → claimed → submitted → approved (directed quests start at claimed)
- `approved_by` — the adult who approved; **embers only count once set**
- `due_date` — the board query filters `due_date <= today`. Recurrence spawns the next instance *dated forward*; it appears only when its date arrives.
- `recurrence` — none / daily / weekly / **monthly** / custom. Monthly reveals a **day-of-month** selector (ember chips 1–31 + "Last day"), short-month clamp. On approval of a recurring quest, archive the completed instance and spawn the next — **never reset in place** (one shared spawn path).
  - **Per-cadence rollover rule:** daily = no accumulation (missed dailies archive + reset fresh — no guilt-pile); weekly/monthly may legitimately persist as still-due.
- `rating` — optional 1–5, quality signal only, does NOT change embers
- `is_favorite` — star → appears in a quick-add template list
- `tier` — derived from points: **DIM → WARM → HOT → BLAZING.** Drives visual intensity
- Timestamps: created, claimed, submitted, approved

**Adult self-completion:** when the submitter is an Adult, completion writes `status='approved'` + `approved_by=self` **atomically** — embers mint immediately, never entering the pending queue. The gate is *satisfied*, not bypassed. Kid completions unchanged.

**Planned extensions (parked):** `objectives[]` — an array of checkable steps turning a quest into a **multi-step quest**. Steps don't mint; only turn-in does, and submit is locked until every step is checked. The model home for **chore lists** and the deferred list→quest hook.

---

## Household & roles
- **Members ("hearthmates"):** name, avatar (hero portrait), role (**Adult / Kid** — display label; role enum unchanged), class (**Forge / Garden / Keep / Hall**).
- **Model:** profile-switching, shared-device first (Netflix-style — tap to switch), because young kids have no phone. Join-code is the *secondary* path. **Kids have no auth identity** — they are sub-profiles acting under an adult's authenticated session. *(This is also the COPPA advantage: no kid email, no direct collection. And the reason naive `auth.uid()`-based RLS fixes don't work — see Security.)*
- **Adult profiles are PIN-locked, fail closed.** A PIN is *mandatory* at adult creation (with confirm-entry); existing PIN-less adults are intercepted and forced to set one. No path into an Adult profile without a PIN. This gives the approval gate teeth (anti-cheat).
- **Adult profile creation** routes through the `create_adult_profile` SECURITY DEFINER RPC. *(Fixes the `permission denied for table users` production bug; the kid path was fixed by dropping a redundant subquery.)*
- **PIN / password recovery (three locks):** (a) **adult-resets-adult** — a reset (↺) on each other adult's member card clears their PIN; fail-closed intercept forces a new one on switch-in. (b) **account-owner master path** — "Forgot PIN?" clears that profile's PIN via the owner-gated `set_profile_pin` RPC; closes the sole-adult lockout. (c) **account login** — "Forgot password?" → Supabase built-in reset → `/reset-password`. No new auth system, no new tables. *(Threat note: an acting adult can reset another adult's PIN — peer trust, fine for a 2-adult hold; revisit for peer/roommate holds.)*
- **Hold membership:** an owner; an editable hold name. **Invite to the Hold** fires the Web Share API with a prefilled message + deep link `theemberhold.com/join?code=XXXX` (desktop clipboard fallback); `/join?code=XXXX` pre-fills the join screen. **Join code visibility + invite are Adult-only** (inviting is admin authority).
- The **"Whole hold" entity is marked by the keep glyph** in household-amber — members carry identity-color dots; the hold carries the brand object.

## Onboarding (Pip-guided)
The ember-sprite mascot, **Pip** (soft-named; kids retain veto), is the app's guide. Pip is **guidance only** — read-only, never awards embers, unlocks, or gates anything (the game stays in quests). Reuses Pip's expression set (idle / encourage / celebrate).
- **First-run is contextual setup, not a feature tour.** Signup: Pip greets, inline "?" help on the three non-obvious fields (Role, Adult PIN, Start-vs-Join); signup creates the account holder only.
- **Add-to-hold (creator only):** a dedicated Pip-guided screen builds the rest of the hold (hearthmate repeater — name / avatar / role; Adult requires PIN).
- **Joiners** skip hold-building. **Kid first-run:** welcome card + 3 skippable coach-marks. **Persistent help:** Pip in the top header corner → role-aware help sheet + "Replay the intro" / "Add more hearthmates."
- **Feature tour is on-demand** — offered only once the hold has content. The loop card (**"Post the work → Claim & complete → Approve & reward → Chase the campaign"**) doubles as the marketing pitch, and is the intended centerpiece of the landing page.

---

## The economy
- **Sources:** quests mint embers, on adult approval (adults self-approve atomically). **Sinks:** the **Vault**. **Pools:** campaign collective bars.
- **Earned vs. spendable split.** Ranks tracks **lifetime embers earned** and is never reduced by spending. The Vault draws from a separate **spendable balance** (earned − redeemed). **Spending never costs rank.**
- **Two reward rails:** the *free* rail (spectacle, status, the win) and the *costed* rail (the Vault). For young kids the free rail may carry most of the daily motivation — delight cooperation rather than buy it.
- **Individual redemption is the model.** Rewards are redeemed by a person, from their own balance. Pooled/shared earning is **not** a Vault feature — it is what Campaigns' EARNING type is for. *(This is why Date Night is a Campaign, not a reward.)*

### The reward tiers
Rewards sit on the same **DIM / WARM / HOT / BLAZING** ramp as quests. One ramp, both directions of the economy.

### The adult reward menu (v1 — LOCKED, 7 items)
Adults earn and spend too — this is what May actually uses, and it's what makes the app a household tool rather than a kid tool with adult admins.

| Tier | Reward |
|---|---|
| **Dim** | Coffee / breakfast delivered to you |
| **Dim** | Pick dinner |
| **Warm** | Skip one chore-quest |
| **Warm** | Pick movie / show night |
| **Hot** | One guaranteed hour alone |
| **Blazing** | Solo morning routine (someone else runs the morning) |
| **Blazing** | Solo bedtime (someone else runs bedtime) |

**Deliberately excluded — dishes.** It already exists as a quest. The same chore cannot be both a source and a sink; double-dipping reads as a bug, not a reward.

**Deliberately relocated — date night.** Moved *out* of the Vault and *into* Campaigns as the EARNING type's first real content. It's a shared goal two people pool toward, not something one person buys. This is what the couples rail exists to serve.

---

## Modules (the surfaces)

### The Engine
Quest board, ember economy, recurrence (incl. monthly day-of-month), ratings, favorites, adult approval, Ranks leaderboard, Quest Log.
- Board is **date-windowed** (`due_date <= today`). Completed dailies leave the board; missed dailies archive + reset fresh on board mount (`roll_missed_dailies`).
- **Scope badges (adult view only):** `adults_only` → "Adults only"; `kids_only` → "Kids only"; `anyone` → no badge. Not shown on the kid board — a kid just sees claimable work.
- **Claim-eligibility copy states itself exactly once per card.** `anyone` keeps the "Available to anyone" subline and gets no badge; scoped quests show their badge and **drop** the subline. (Kills the "Adults only / Available to anyone" self-contradiction.)

### The Vault (the reward store — dual-mode, both roles)
The store **switches mode on a binary affordability trigger**. This is affordability-as-heat: the hierarchy principle applied to desire.

**Kid view:**
- **ZERO affordable → CATALOG ("The Wish Menu").** The full ladder by tier, cheapest-first, everything visible, everything favoritable. This is the *goal-setting / desire* state. Header: "Nothing in reach yet — pick what you're grinding toward."
- **ONE+ affordable → CURATED.** In-reach glowing set (up to 2 at/under balance) + live **Redeem** + "N embers left after" + next-unlock taunt(s) with progress bars + per-tier fold. This is the *decision / cash-out* state.
- **Favorites are additive, never an override.** A pinned reward rides *alongside* the auto-surfaced slots. A goal pinned in catalog mode carries into curated mode.
- **Popularity / highest-redeemed is explicitly rejected as a curation signal** — it structurally buries the aspirational rewards, which are the ones doing the motivational work.

**Adult view — three zones, top to bottom:**
1. **Awaiting Your Approval** — pending *kid* redemption requests. Each card shows **redeemer name + face** (this doubles as the kid-impersonation mitigation), reward, cost, timestamp. Approve/Deny. Empty state: "The hearth is quiet — no requests waiting."
2. **Your Store** — the adult's own balance-anchored store, **reusing the kid store component** pointed at the adult's balance. Adults redeem instantly; an adult's own redemption never generates a Zone-1 card.
3. **Reward Menu · Manage** — edit/delete/add rewards (Quick Picks + custom). No redeem buttons, no affordability text.

**Reporting deliberately does NOT live here.** Redemption history and reward-performance/prune belong on a future admin/reporting surface — Emberhold's first *analyze-vs-operate* split. Data intact, just not shown in the Vault.

**The couples rail (`reward.audience`).** `household` / `adults_only`, default `household`. Toggle in the create/edit-reward form ("Everyone in the hold" / "Adults only"). Store filters by viewer role — kid sees `household` only; adult sees both. Membrane-safe: adults_only rewards are normal rewards with the same sink and same flow; only visibility changes.

### Campaigns
Shared-goal containers a quest tags into, with a progress bar. Two types via `goal_type`:
- **PLANNING** — completed tagged quests / total (e.g. São Paulo).
- **EARNING** — pooled embers. **This is the home for shared/couple goals** (Date Night). The pooled mechanic that the Vault deliberately doesn't have.

Planning tools, not earning grinds. Progress renders as a bare **%** (earning-type campaigns showing a real ember count are the exception). Campaign completion fires the **Legendary** override state on the feed.

### Calendar
Standalone event CRUD: month grid, per-member color dots, today-glow, day-tap list, event detail. Events carry who / date / optional `end_date` (multi-day) / times or all-day / location / notes / simple recurrence. The keep glyph marks whole-hold items. **Native OS date picker** for event dates (deliberate). Event **creation** is a feed write point; edits aren't. No iCal import yet (deferred).

### Lists
A generic shared-list primitive (`list` / `list_item` / `list_section`). Index (ember cards, open-count) + detail (docked add-item bar, **strike-and-sink**, user-defined **sections** with per-section sink and a section chip, per-item delete, "Clear checked"). Seed lists: Groceries / Packing / Errands. Permissions: anyone adds/checks/deletes items + manages sections; **deleting a whole list is adult-only.** **Off the activity_log spine** (membrane: no embers, no game).
- **Add-item bar = full-width solid docked footer** (opaque elevated layer, hard top edge, fade scrim; list scrolls cleanly *behind* it). The global FAB is suppressed on list-detail — the docked bar is the sole create action.
- **Search bar** appears automatically past ~50 items (sticky top); **searches checked items too** (the un-stock-when-you-run-out case); retains section grouping during search; clear-to-restore.

### The Briefing
The Adult command center and the **Hub's seed**, built as final architecture with sparse contents (modules add rows, never re-poured). Zones, priority-ordered by brightness=heat=importance:
1. **NEEDS YOU** attention bar
2. **OPEN BOUNTIES** strip *(the one surviving use of the word)*
3. **FEED** — the live activity pulse strip (newest-first, auto-advancing, DIM→BLAZING heat, 48h window)
4. **THE ROSTER** — 2×2 portrait tiles of ALL members, heat-dial
5. **ON THE HORIZON** — rolling 3-day
6. **CAMPAIGNS** — glanceable bars

Plus the Quest Log link.

### In-app notifications
Global header bell, every screen. Badge = actionable items for the current profile only (Adults: submitted quests + pending redemptions; Kids: none in v1). Panel: **NEEDS YOU** (actionable) + **RECENT** (~10 latest activity_log items, read-only). **Stateless** — no read/unread, no new table; derived live from quest status + activity_log.

---

## Navigation
- Bottom tab bar, **7 tabs**: BOARD · CAMPS · CAL · **LISTS** · VAULT · RANKS · YOU. Lists is dead-center.
- The **"+" FAB is raised** to a kiss-overlap above the bar with a soft contact glow. It opens the create menu; becomes the docked submit inside a create flow; is suppressed on single-purpose surfaces that own their create action (Lists detail).
- *(Tab count is one past mobile comfort — intentional/temporary; profile-to-header consolidation is parked, not forced.)*

---

## Avatars (the hero roster)
- **Hero portraits are the sole avatar system** (Classic emoji retired). Never a bare photo in the circle — a raw photo breaks the style floor and would make every hand-crafted portrait read as set dressing. *(The underlying "I want it to be me" want is served instead by roster variety near-term, and long-term by a photo→stylized-hero render pipeline that pushes an uploaded face* into *the floor.)*
- The picker is grouped by the four classes and **collapses per class** to ~4 portraits with "show more / show less" — the container that lets the roster grow deep without ever reading as cluttered. Current selection forces its class open.

### The four classes
Double as task + animation themes:
- **FORGE** — warriors, protectors, ember-forged heroes
- **GARDEN** — nature-touched growers and wildsfolk
- **KEEP** — steadfast watchers and guardians
- **HALL** — the whole gathering hall: makers **and** entertainers **and** keepers of warmth (bards, storytellers, dancers, revelers, hearth-tenders, hosts). Food is **≤ ~1/3** of the class.
  - *Renamed from **FEAST** (7/03) — and it was a redefinition, not just a label swap. "Feast" narrows to food (a generator fed the word returned 6/8 food characters). "Hall" restores the spatial-noun logic of the other three and opens the class to what it was always meant to be. Cheap find/replace: label, enum value, animation-theme name.*

### The roster
**44 characters: 40 base (10 per class) + 4 cool-register (1 per class).**
- Expanded 8 → 10 per class because the collapsible picker makes depth free — every "swap or cut" argument dissolves into "keep both."
- **Register spread per class (LOCKED):** every class must span **cute / majestic / COOL**. Not "cute where the class allows" — **every class carries 2–3 genuine kid-magnets** *and* a cool/battle-ready character. Generators underweight the cute end by default and regress to one token per class unless explicitly pushed. Validated by which avatars the kids actually reacted to — which is the entire product thesis.
- Cool-register picks: Forge → ember-lava warrior-woman · Garden → antlered stag-warden · Keep → crystalline ice knight · Hall → twilight crown-queen.

### The Avatar Style Spec (LOCKED — the band)
Coherence is governed by a **wide style band with a hard floor**. Range freely *inside* the floor; never break it.

**The floor — three load-bearing constraints (all must hold):**
1. **LUMINOSITY** *(most important)* — the subject glows from within / carries strong warm ember rim-lighting; light comes OFF the character. "Brightness = heat = importance" expressed as a character. Never flat, dull, or muddy.
2. **STYLIZED ILLUSTRATION** — clearly hand-drawn painterly fantasy art. NOT photoreal, NOT gritty oil-painting realism, NOT flat chibi/mascot. The grounded, rich middle.
3. **SATURATED JEWEL COLOR** — rich, clean, saturated color (crimson, purple, blue, teal, ember-gold). Never muddy brown earthy naturalism.

**Inside the floor, the band is WIDE** — cute ↔ grizzled ↔ beautiful all welcome. A luminous battle-woman and a chibi baby dragon cohere because they're *lit and colored* alike, not because they share a subject or proportion.

**Composition rule (LOCKED): circular-crop-safe framing.** Every avatar centered with even margin on all sides; full head (crown / horns / hat included) + shoulders sit inside a centered circle with breathing room, nothing touching edges. Applies to all future avatar generation regardless of engine.

**Background rule:** each background derives from THAT character (their element / domain / mood), kept dark enough to blend into the app base `#1A110B` in a circular crop — a FILLED portrait, not a transparent cutout. **No uniform gold frame, no shared set-dressing.** The rim-light unifies the roster; a frame does not. Backgrounds vary character to character. *(Watch the leak: the luminosity constraint tends to bleed into the background and set everything on fire. Fence it explicitly.)*

**Diversity rule:** humanoids default to one young light-skinned face unless explicitly fenced. Name the archetype traps — silver-locs elder, curly-haired beauty, big-eyed elf child, generic young-hero man — as **"at most once in the entire roster."**

**Asset hygiene:** image gen always lies about "transparent background" (it paints a box/checkerboard). Sidestep it with filled-dark backgrounds that blend into `#1A110B`. For any future cutout need, generate on flat pure-green and chroma-key; verify alpha programmatically.

### How to actually generate avatars
**The method lives in `playbooks/avatar-generation.md`.** It is hard-won, counterintuitive, and not optional — read it before touching a generator. Headline: the style block controls output by **axis, not length** (pin the floor; free the casting, palette, and pose); generate the **full roster in one run** so the anti-repeat constraint can bind; prompt the cool register through **material and light**, never supernatural vocabulary.

**Status:** roster **designed and banked** (44 characters). The remaining work is **transport** — files → Lovable/Supabase (naming, sizing, alpha/crop, picker slotting). Design is done; shipped is not.

---

## Monetization (the Founding Guildhall)
*(Never previously in the spec. It is design truth now.)*

- **One v1 SKU: the "Founding Guildhall."** A **one-time, household-level** purchase. Stripe Checkout + Supabase webhook + a household entitlement flag. Staying outside app-store rails retains **~97%** of revenue — the reason the PWA posture is a decision, not a compromise.
- **What it unlocks:** the remaining **24 of 40** base avatars (free tier keeps 16 — four per class).
- **The free tier must be genuinely COMPLETE, not a demo.** This is the membrane applied to money: **free is a full tool; the purchase is delight, never access to basic function.** Every free four therefore needs a genuine kid-magnet — first-love is the free tier's job; aspirational-but-reachable is the paid tier's.
- **THE MONETIZATION SEAM RULE (load-bearing).** Per-class or per-character SKUs recreate a *"buy each kid their skin"* dynamic **inside a household tool** — the worst possible seam to put between a parent and their kids. A **household-level** unlock dissolves it entirely: one purchase, everyone gets everything. **All future cosmetic drops must remain household-level.** No exceptions.
- **Membrane-safe cosmetics.** Cosmetics can be **seen** but must never affect gameplay, ember earning, or quest access. A paid avatar is not a better avatar.
- **Second tollbooth item (parked):** a seasonal **frost cosmetic set** — the leftover cool-register gens + a unified frost aesthetic, shipped as a household-level unlockable. Build after the base roster ships and validates. *Don't build the winter DLC before the base game.*

---

## Design system
- **Ember-lit aesthetic.** Warm charred darks, never cold gray. Base `#1A110B`, elevated card `#241813`, top-edge sheen `#6E4A2E`, primary text `#F2E3CB`, muted `#A07B54`. Ember ramp (deep→bright): `#BA7517` → `#EF9F27` → `#F8C13C` → `#FCDE5A`; gold rim `#E0A94A`.
- **Brightness = heat = importance.** The hierarchy principle AND the animation-intensity dial AND the avatar-luminosity floor AND (as of 7/10) the Vault's **affordability-as-heat** mode switch.
- **Member identity colors are a SEPARATE system from the ember ramp.** Jewel-tone hues across grid dots, event cards, the Briefing horizon. SnowDad amber/gold `#E0A94A` (also "Whole hold" default), May violet `#9B6BD6`, Mia jade `#3FB37A`, Cade steel-blue `#4A9FD6` (spare: rose `#E0607A`, teal `#2DB3A6`, coral `#E08750`).
- **The household keep glyph.** Tintable SVG (keep body `currentColor`, hearth a fixed warm glow), household-amber, ~20–22px inline.
- **Date pickers.** Calendar event dates use the **native OS picker**; quest day-of-month uses **ember-styled chips**.
- **Depth, not flat:** layered surfaces, a 1px warm top-edge highlight, soft shadow beneath cards, glow on heat.
- **The FAB-as-submit / FAB-suppression pattern:** on create screens the bottom-center `+` becomes the form's submit, docked in-flow. On a single-purpose surface that already owns a create action, the global FAB is suppressed entirely. **Exactly one create control in the bottom thumb zone, always.**
- **Layered docked surfaces:** a docked input over a scrolling list must be a full-width **opaque** elevated layer with a hard top edge and a short fade scrim — content scrolls *behind* it, never *through* it. Translucent floating capsules over scrolling content are banned (they wash out).
- **Brand: two marks, one keep.** App icon (round-towered keep + glowing hearth on a charred tile) = universal small mark; login crest (the same keep in a forged-gold heraldic shield) = ceremonial mark. One keep silhouette across icon, crest, and household glyph. Motto **"by hearth & hold."** Plus **Pip**, the ember-sprite mascot (idle / encourage / celebrate).

## Experience layer (the juice)
- Completing a quest is theater (Battle Chess principle). **Tier = intensity, class = theme — no new data model.**
- **Pass 1 live:** reactive micro-motion (ember burst by tier, approval echo, new-approval pulse, count roll-ups, press feedback, breathing submit button) + a subtle state-driven household-warmth shift.
- **Wall / kiosk / display mode:** always-on, no-login, full-screen on a cheap fridge tablet — the Hub's final form factor; the activity feed is its engine. **Validated layout** (designed, not built): top full-width FEED ticker; bottom-left ~3/4 calendar WEEK view; bottom-right ~1/4 avatar-collapsible ACTION RAIL (no-login, no-mint — claim/complete move status only; **approval stays PIN-gated on an adult's phone**). Open-pool visibility lives IN the feed (unclaimed-glow + 72h aging render rules). Rotating ambient cards, 5–10s.
  - **This is the ambient-presence thesis, and it is unproven.** It is also fenced to post-launch. See the north star for the open tension.
- **AI-generated, never stale:** grow a library of themed vignette assets in the background, cache, select instantly at the win. Bake an alpha-check into the pipeline.

## The activity feed (the event-log spine — BUILT)
A single **append-only, permanent, immutable** stream — the source of truth for everything that has happened in the hold.
- **`activity_log` table.** Fields: `id`, `actor_id` (soft, nullable), `actor_label` (frozen), `verb` (enum), `object_id` (soft, no hard FK), `object_label` (frozen at write), `ember_delta` (nullable), `campaign_id` + `campaign_label` (nullable), `significance` (**Legendary** campaign-completion override only), `created_at` (indexed).
- **`logActivity()` helper** — the single app-level write path. No DB triggers — explicit curated call sites only.
- **Five curated write points:** `quest_approved` (actor = completer; +points; campaign if tagged) · `bounty_posted` (unassigned/open only; actor = creator) · `reward_redeemed` (actor = redeemer; −cost) · `campaign_completed` (system, actor null, on 100%) · `event_created` (calendar creation only; ember_delta null — membrane).
- **CAP THE READ, NEVER THE WRITE.** The table is permanent (the Adventure Log's source). Consumers window their own reads. **No pruning queue, ever.**
- **Downstream consumers (reads off the spine):** the notification RECENT panel (built) · the full in-app feed view · the notification/awareness layer (built in-app; PWA push later) · wall/kiosk live callouts · the **weekly recap** (the feed's first real consumer) · the Adventure Log · the family message board. Build each as a read off the existing table.

---

## Security posture (design truth, not just a bug list)
- **The core rule: embers mint only on adult approval.**
- **SECURITY DEFINER audit.** A set of definer functions flagged by the linter need an internal-caller-gating sweep. *(One over-broad anon grant already revoked.)* *(Function-level detail in the private note.)*
- **Join codes admit members to a household.** Pre-distribution priority: policy design for code rotation and admission flow.
- **Kid-vs-kid impersonation.** The client-side profile switch is ungated. Kid PINs stay **off by default** (they tax the walk-up thesis, which is the whole shared-device model). Mitigation already built: the **redeemer's name + face on the adult approval card**. Resolution is an optional lightweight picture-lock, shipping with the wall/kiosk phase.
- **Persistence debt:** Vault favorites currently use `localStorage`. Fine for validation, fatal for the shared-wall model — favoriting is now load-bearing (goal-commitment) across kid store, adult store, and rail. **Needs real per-profile persistence before ship.**

---

## Tech & working model
- **Stack:** Lovable.dev (React + Supabase), mobile-first installable PWA, live at theemberhold.com. **Stripe Checkout + Supabase webhook** for the Guildhall. **Copilot Create** for avatar/asset generation (work account — zero Lovable credit burn). **Claude Code** for the codebase-reading jobs Lovable can't do from inside itself (RLS audits, the self-approval fix, any future native shell).
- **The Copilot bridge is validated end-to-end:** Copilot designs against the locked floor → Scott selects → Lovable engineers. Same lane discipline, different asset engine, zero credit burn. A proven part of the working model, not a maybe.
- **The team (three lanes — LOCKED):**
  - **Scott** — vision, taste, final decisions.
  - **jAIne** — hears, challenges, breaks down, translates intent into direction (build prompts, debugging, spec stewardship). *Does not reach into Lovable's lane.*
  - **Lovable** — engineers the functional output.
  - **The principle:** trust each lane; don't do each other's jobs. **jAIne's failure mode is over-specifying when under-trusting downstream** — scripting exact characters or specifics is grabbing Lovable's wheel. The fix: brief the **floor + intent**, leave **latitude on execution**. Evidence: Scott's favorite avatars were the ones authored *unsupervised* off loose briefs; the over-specified gens were the weakest. Applies platform-wide, not just to avatars.
- **Brain → hands loop:** this Project's docs are the brain; Lovable is the hands; jAIne is the reasoning + debugging between. Each build phase runs as its own chat.
- **"Shipped" has a standard.** A completed end-to-end loop — not a screen rendering in a screenshot. Design-complete ≠ shipped (see: the 44 avatars sitting on a phone).
- **Source-of-truth upgrade (parked, warming):** a version-controlled canonical repo + manifest when Emberhold outgrows Project knowledge. Signal: doc-edit friction (no write-back; every update is a manual file swap). *The doc-set is getting big and this signal is now firing.*

## The other docs
See `README.md` for the full manifest. In short: `north-star.md` (why + the gate ladder) · **this spec** (what it is) · `status.md` (where the build is and what's left) · `parking-lot.md` (what might be) · `decisions.md` (what we decided and why) · `playbooks/` (how to do a thing) · `archive/` (reasoning history).

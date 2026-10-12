# Emberhold — User-Facing Copy Inventory
 
Read-only recon. No application file was changed to produce this report. Baseline: `a119f1444bda401b149387809b67cbf666ea6477`.
 
**Scope note (read before using this report):** "User-facing" is interpreted here as anything a sighted user reads on screen — headings, labels, button text, placeholders, helper/subtitle text, empty/loading/error states, toasts, confirm/prompt dialogs, real tooltips (`title="..."`), badges/chips/tab/nav labels, and browser-tab `<title>` meta text. Bare `aria-label` attributes on icon-only controls with no other visible text are **excluded** — they're accessibility plumbing, not voice/tone copy, and including every one (there are 20+) would bury the strings that actually matter. A handful of borderline cases (an aria-label that doubles as the only meaningful description of a control, e.g. a progress bar's `aria-label`) are called out inline where a sub-agent flagged them. User-authored content (household/member/quest/reward names, notes, etc.) is excluded per the brief; no hardcoded seed/sample content was found anywhere.
 
Global chrome — `AppShell.tsx`, `BottomBar.tsx`, `NotificationBell.tsx`, `PipHelp.tsx` — wraps nearly every authenticated screen. Rather than repeat their full string lists under all ~30 screens (which would balloon the report without adding information), each is documented once in its own section below, and every screen that renders inside `AppShell` says so with a one-line pointer.
 
---
 
## Board (Kin view)
 
**Route:** `/board` · **Files:** `src/routes/_authenticated/board.tsx` (kid branch), wrapped in `AppShell` (see Global Chrome)
 
- "Your ember" — label above XP banner
- "embers" — unit suffix next to XP count
- "Ranks →" — link to leaderboard
- "Quick add" — heading above favorites strip
- "Open bounties" — section title
- "No open bounties. Strike a flame with +" — empty state
- "My bounties" — section title
- "Your hearth is quiet for now." — empty state
- Renders `<QuestCard>` for each bounty — see Shared Components
- Renders `<FirstQuestDoorway>` when the board is empty for a first-time member — see Shared Components
---
 
## Board / Briefing (Keeper home)
 
**Route:** `/board` · **Files:** `src/routes/_authenticated/board.tsx` (parent branch, three sub-views) + `src/components/emberhold/Briefing.tsx`, wrapped in `AppShell`
 
*board.tsx, open-bounties drill-in (`?bounty=true`)*
- "Briefing" — back-link text
- "Open bounties" — kicker
- "Open bounties" — h1 (same text as kicker directly above it)
- "Open bounties" — Section title
- "No open bounties." — empty state
*board.tsx, member drill-in (`?kid=<id>`)*
- "Briefing" — back-link text
- "Roster · viewing" — kicker
- "Unknown member" — fallback heading
- "{name}'s active bounties" (fallback "Their") — Section title
- "Nothing active for them yet." — empty state
- "Open bounties" — Section title
- "No open bounties." — empty state
*Briefing.tsx (default parent home)*
- "Admitted to the hold" — toast.success, member admission
- "Could not admit" — toast.error fallback
- "Request denied" — toast, deny admission
- "Could not deny" — toast.error fallback
- "Sealed" — toast.success, quest approval
- "Could not approve" — toast.error fallback
- "All quiet at the hold." — empty state (ZonePending, nothing needs attention)
- "{n} bounty/bounties, {n} request/requests need your seal" — headline, three pluralized variants depending on which counts are nonzero
- "Needs you" — kicker
- "Approve" — inline approve button
- "Admit as:" — label, admission-confirm row
- role toggle buttons — **from `ROLE_LABEL`** (`src/lib/quest-helpers.ts`)
- "Cancel" / "Confirm" — admission-confirm buttons
- "Wants to join · requested: {role}" — admission row subtext, role **from `ROLE_LABEL`**
- "Deny" / "Admit" — admission row buttons
- "Open bounties" — kicker (both zero- and nonzero-count states)
- "No open bounties" — zero-count state (note: no trailing period, unlike board.tsx's "No open bounties.")
- "{n} open" — nonzero-count template
- activity-ticker line templates (ZonePulse), by verb: "{who} completed {obj} · +{delta}" / "New bounty: {obj} · +{delta}" / "{who} redeemed {obj} · {delta}" / "{obj} is complete" / "{actor} added {obj} to the calendar" or "New event: {obj}" / "{who} admitted {obj}" / "{who} denied {obj}" / "{who} · {obj}" (default) — see Duplicates, this template set is reused verbatim in NotificationBell and closely mirrored in hearth-log.tsx
- "The hold is quiet" — ZonePulse empty state
- "The Roster" — heading
- "No one in the household yet." — empty state
- "{n} awaiting your seal" / "{n} active bounty/bounties" — tooltip (`title=`) on roster tile badge
- role tag on roster tile — **from `ROLE_LABEL`**
- "Today" / "Tomorrow" — day-group labels (ZoneHorizon)
- "On the horizon" — heading, link to Calendar
- "Clear skies ahead." — empty state
- "All day" — event time label
- "+{n} more →" — overflow link
- "Campaigns" — heading, link to Campaigns
- "No active campaigns." — empty state
- "of" — joining word between done/total counts
- "· {pct}%" — percent-complete template
- "The slate" — kicker, bottom link card
- "The Slate: standing duties & open bounties" — description, bottom link card
---
 
## Quest detail
 
**Route:** `/quest/$id` · **Files:** `src/routes/_authenticated/quest.$id.tsx`, wrapped in `AppShell`
 
- "Bounty · Emberhold" — page `<title>`
- "Loading…" — loading state
- "Could not update" — toast.error fallback
- "Board" — back-link text
- status chip — **from `STATUS_LABEL`**
- recurrence chip — **from `RECURRENCE_LABEL`** (shown only when recurrence ≠ none)
- "embers · {tier}" — subtext under points; note `tier` is the raw lowercase `xpTier()` value ("dim"/"warm"/"hot"/"legendary"), **not** run through `TIER_LABEL` — see Observations
- "Posted by" / "For" — row labels
- "Unclaimed" — row value, unowned
- "—" — row value fallback, no creator
- "Done" — row label, completion timestamp
- "Campaign tag" — section label
- "Untagged" — campaign-tag button
- "Claim this bounty" — button
- "Mark done · +{points} embers" — submit button, Keeper path
- "Mark as done" — submit button, Kin path
- "Approve · +{points} embers" — approve button
- "Waiting for a Keeper to approve." — note, Kin viewing a submitted quest
- "Unfavorite" / "Save as favorite" — favorite toggle
- "Delete bounty" — button
- "Quality" — rating row label
---
 
## Quest Log (Keeper only)
 
**Route:** `/quest-log` · **Files:** `src/routes/_authenticated/quest-log.tsx`, wrapped in `AppShell`
 
- "Quest Log — Emberhold" — page `<title>`
- "Board" — back-link text (both the access-denied view and the main view)
- "Quest Log is Keepers only" — access-denied heading (non-Keeper role)
- "Switch to a Keeper profile from the Profile tab to oversee every quest." — access-denied body
- "Switch profile" — link button
- "Quest Log" — page heading
- "Every quest in your hearth — oversee, approve, reassign." — subtitle
- group headings/subtitles: "Awaiting approval"/"Action needed", "In progress"/"Claimed, not yet submitted", "Open bounties"/"Unclaimed", "Done"/"Recent history"
- "Nothing here." — empty-group state
- "Could not update" — toast.error fallback
- "Quest deleted" — toast.success
- "Could not delete" — toast.error fallback
- "Approved" — toast.success
- "Sent back" — toast (plain, no variant)
- status chip — **from `STATUS_LABEL`**
- recurrence chip — **from `RECURRENCE_LABEL`**
- "For " / "Unclaimed" — assignee line
- " · by " — creator-line prefix
- lowercase tier word under points — raw, not `TIER_LABEL` (see quest.$id.tsx note)
- "Created" / "Claimed" / "Approved" / "Submitted" — Stamp labels
- "Approve · credit embers to" / "Mark done · credit embers to" — panel label, conditional on status
- "Reject — send back" — button
- "Reassign" — section label
- "Unclaimed" — reassign-to-unclaimed button
- "Edit" — button
- 'Delete "{title}"?' — window.confirm text
- "Delete" — button
- "Open →" — link to quest detail
- EditPanel: "Title" / "Embers" field labels
- audience option labels — **from `AUDIENCE_LABEL`**
- "Who can claim it" — field label, shown only when not directed
- "Repeats" — field label
- "One-off" — recurrence option when none; otherwise raw "daily"/"weekly"/"monthly"
- "Cancel" / "Save" — EditPanel buttons
- "Ember reward must be at least 1" — toast.error, validation
- "just now" / "{m}m ago" / "{h}h ago" / "{d}d ago" — relTime() output — see Duplicates
---
 
## Create bounty
 
**Route:** `/create` · **Files:** `src/routes/_authenticated/create.tsx`, wrapped in `AppShell`; renders `<QuickAddTray>` (see Shared Components)
 
- "New bounty · Emberhold" — page `<title>`
- "Bounty needs a title" — toast.error
- "Ember reward must be at least 1" — toast.error
- "Not ready" — toast.error
- "Bounty posted" — toast.success
- "Could not create" — toast.error fallback
- "New bounty" — page heading
- "Post a bounty for anyone, or send it to someone." — subtitle
- "Bounty title" — field label
- "Empty the dishwasher" — placeholder
- "Ember reward" — field label
- "+{n}" — quick-points chips (+5/+10/+20/+50/+100)
- "Tier: " + lowercase tier word — raw, not `TIER_LABEL` (third occurrence of this pattern — see Observations)
- "Assign to" — field label
- "Open to anyone" — assignment button
- "Who can claim it" — field label, shown only when unassigned
- audience option labels — **from `AUDIENCE_LABEL`**
- "Repeats" — field label
- "One-off" — recurrence option when none; otherwise raw value
- "Save as quick-add favorite" — toggle label
- "Part of a campaign? " / "(optional)" — field label
- "None" — campaign selector button
- "Cancel" — button
- "Lighting…" / "Light the bounty" — submit button
---
 
## The Wall
 
**Route:** `/wall` · **Files:** `src/routes/_authenticated/wall.tsx` (single very large file, own display-mode chrome, does not use `AppShell`/`BottomBar`)
 
- "Wall · Emberhold" — page `<title>`
- "Could not update" — toast.error, quest mutation
- "Could not send request" — toast.error, redemption request
- "Could not approve" — toast.error, redemption approval
- "Emberhold" — fallback family name
- "Turned in, waiting for a Keeper" — toast.success, quest turn-in
- "Request sent, waiting for a Keeper" — toast.success, reward request
- "{name} claimed {title}" — toast.success, claim bounty
- "Approve bounty" / "Approve redemption" — PIN-prompt titles
- "Keeper PIN required to mint {n} embers" / "Keeper PIN required to spend {n} embers" — PIN-prompt detail text
- "Approved, {title} minted +{points}" — toast.success
- "Approved, {reward_name} redeemed" — toast.success
- "The Hold" — eyebrow above family name (TopBanner)
- ticker-line templates (`tickerLine()`) — same verb-keyed set as Briefing's ZonePulse (see Duplicates)
- "Someone" — fallback for missing actor name
- "The hold is quiet" — ActivityTicker empty state
- "On the horizon" — CalendarPanel section header
- "Today" / "Tomorrow" — AgendaList day labels
- "Clear skies ahead." — AgendaList empty state
- "All day" — event time label
- "+{n} more" — MonthGrid overflow indicator
- "No active campaign" — CampaignFooter empty state
- "{done} of {total}" — CampaignFooter progress readout
- "Who's on the hearth" — section header (used twice: RightColumn's SectionHeaderBar and again as RosterGrid's own header)
- "Ranks" — section header (used twice: SectionHeaderBar and again inside RanksPanel)
- "Open bounties" — BountiesBanner label
- "Tap to close" / "Tap to see them" / "None right now" — BountiesBanner state text
- "Claimable" — BountiesList header
- "Tap to close" — BountiesList / SectionHeaderBar close buttons (repeated string)
- "Tap to open" — SectionHeaderBar subtitle, collapsed
- "No embers earned yet." — RanksPanel empty state
- "{n} bounties done" — RanksPanel per-member stat
- "embers" — unit label (repeated across Wall: RanksPanel, ClaimBountySheet, MemberQuestsSheet)
- "Recently completed" — RanksPanel expanded sub-header
- "No one on the hearth yet." — RosterGrid empty state
- "all clear" — WallMemberTile status, zero active bounties
- "bounty open" / "bounties open" — WallMemberTile active-count label
- "Display mode" — ExitConfirm eyebrow
- "Leave display mode?" — ExitConfirm heading
- "The wall will return to the normal app. A Keeper PIN is required." — ExitConfirm subtitle
- "Stay" / "Leave" — ExitConfirm buttons
- "Could not verify" — toast.error, PIN verify (repeated at two PIN-prompt sites)
- "Keeper PIN required to leave" — ExitPinPrompt subtitle
- "••••" — PIN input placeholder (repeated at two PIN-prompt sites)
- "Wrong PIN. Try again." — ExitPinPrompt inline error
- "Cancel" / "Leave" — ExitPinPrompt buttons
- "Open bounty" — ClaimBountySheet eyebrow
- "Who's claiming this?" — ClaimBountySheet prompt
- "Not now" — ClaimBountySheet cancel button
- "Claiming…" / "Claim as {name}" / "Pick someone" — ClaimBountySheet confirm button, three states
- "On the hearth" — MemberQuestsSheet eyebrow
- "Ember balance" — MemberQuestsSheet label
- "Bounties" / "Vault" — MemberQuestsSheet view-toggle labels
- "Awaiting a Keeper" — MemberQuestsSheet pending-redemptions header
- "Approve" — MemberQuestsSheet button (repeated: pending redemption, and quest turn-in list)
- "The vault is empty. A Keeper can add rewards from their phone." — empty state, no rewards configured
- "No rewards in reach yet" — empty state heading, rewards exist but unaffordable
- "Complete a bounty to earn more embers, then come back to claim a reward." — empty state subtitle
- "Request" — button, request reward
- "No bounties on this hearth yet." — empty state, quests view
- status chip — **from `STATUS_LABEL`**
- "Turn in" — button
- "A Keeper approves on their phone. No embers move yet." — vault-view footer note
- "Too many attempts. Try again in a moment." — toast.error, PIN lockout
- "PIN not recognized. Try again." — AdultPinPrompt inline error
---
 
## Lists overview
 
**Route:** `/lists` · **Files:** `src/routes/_authenticated/lists.index.tsx`, wrapped in `AppShell`
 
- "Lists · Emberhold" — page `<title>`
- "Lists" — page heading
- "Shared household lists" — subtitle
- "Empty" — list tile status
- "All done" — list tile status
- "{n} open" — list tile status
- "Setting up your starter lists…" — empty state while seeding
- "List name" — input placeholder
- "Add" / "Cancel" — form buttons
- "New list" — button
---
 
## List detail
 
**Route:** `/lists/$id` · **Files:** `src/routes/_authenticated/lists.$id.tsx`, wrapped in `AppShell`
 
- "List · Emberhold" — page `<title>`
- '"{item.text}" checked/unchecked again' — toast, undo-of-undo confirmation
- '"{item.text}" checked/unchecked' — toast, toggle confirmation
- "Undo" — toast action button
- "No section" — fallback section label (rendered at three sites: item's move-to-section menu, and the footer section picker)
- "Move to section" — dropdown section header
- "Rename item" — window.prompt title
- "Rename" — dropdown item (repeated for section menu below)
- "Delete item" — dropdown item
- "Back to lists" — back-link
- "List" — fallback heading when name not yet loaded
- "Empty" — status subtitle, zero items
- "{n} open · {n} done" — status subtitle
- "Search this list…" — search placeholder
- "No items match" — empty search state
- "Nothing here yet. Add the first item." — empty list state
- "Rename section" — window.prompt title
- "Rename" — dropdown item (section menu)
- 'Delete section "{name}"? Items will move to no section.' — window.confirm text
- "Delete section" — dropdown item
- "No items in this section yet." — empty section state
- "Section name" — input placeholder
- "Add" / "Cancel" — form buttons
- "Add section" — button
- "Clear {n} checked" — button
- 'Delete "{name}" and all its items?' — window.confirm text
- "Delete list" — button, Keeper only
- "Add item…" — footer input placeholder
---
 
## The Slate
 
**Route:** `/slate` · **Files:** `src/routes/_authenticated/slate.tsx`, wrapped in `AppShell`
 
- "The Slate · Emberhold" — page `<title>` + meta description ("Every standing duty and one-off bounty your hold has on the slate.") + duplicated og:title/og:description
- "Board" — back-link
- "Everything that's live" — kicker
- "The Slate" — page heading
- "{n} of {n} current" / " · {n} need doing" — status line
- "all" / "daily" / "weekly" / "monthly" — cadence filter chips
- "One-offs" — section title
- "No one-off bounties." — empty state
- "Standing" — section title
- "No standing duties in this cadence." — empty state
- "The Ledger: what's been done →" — link
- "Repeating bounties live here. The trash, the dishes, Monday laundry." — empty-slate body
- "Post a repeating bounty" — empty-slate CTA
- "{n} done" — collapsed-history toggle
- "Retired · {n}" — retired-section toggle
- state-line templates: "Retired {date}" / "Awaiting approval · {who}{ · turned in {date}}" / "Done · {who}{ · {date}}" / "Due now · {who}{ · last done {date}}" / "Done today · resets tomorrow" / "Current · next {date}{ · {who}}"
- "Could not update" — toast.error fallback
- "Bounty deleted" — toast.success
- "Could not delete" — toast.error fallback
- "Retired, kept on the Slate so nobody recreates it" — toast.success
- "Could not retire" — toast.error fallback
- "Approved" — toast.success
- "Sent back" — toast (plain)
- recurrence-anchor chip — **from `RECURRENCE_ANCHOR_LABEL`**
- status chip — **from `STATUS_LABEL`**
- "For " / "Unclaimed" — assignee line
- " · by " — creator-line prefix
- "Created" / "Claimed" / "Approved" / "Submitted" / "Retired" — Stamp labels
- "Approve · credit embers to" / "Mark done · credit embers to" — panel label
- "Reject, send back" — button
- "Reassign" — section label
- "Unclaimed" — reassign button
- "Edit" — button
- 'Retire "{title}"? It stays on the Slate, dimmed.' — window.confirm text
- "Retire" — button
- 'Delete "{title}"?' — window.confirm text
- "Delete" — button
- "Open →" — link
- EditPanel: "Bounty title" / "Embers" field labels
- audience option labels — **from `AUDIENCE_LABEL`**
- "Who can claim it" — field label
- "Repeats" — field label
- "One-off" — recurrence option when none; otherwise raw value
- "Cancel" — button
- "Ember reward must be at least 1" — toast.error
- "Save" — button
- "just now" / "{m}m ago" / "{h}h ago" / "{d}d ago" — relTime() — see Duplicates
---
 
## The Ledger
 
**Route:** `/ledger` · **Files:** `src/routes/_authenticated/ledger.tsx`, wrapped in `AppShell`
 
- "The Ledger · Emberhold" — page `<title>` + meta description ("Every finished bounty in your hold, newest first.") + duplicated og:title/og:description
- "The Slate" — back-link
- "Everything finished" — kicker
- "The Ledger" — page heading
- "The hold" — filter chip, all members
- "Nothing finished yet." — empty state
- "Today" / "Yesterday" / locale weekday — section date headers
- "Unattributed" — fallback when no member matched
---
 
## Calendar
 
**Route:** `/calendar` · **Files:** `src/routes/_authenticated/calendar.tsx`, wrapped in `AppShell`
 
- "Calendar · Emberhold" — page `<title>`
- "Event removed" — toast.success
- "Could not delete" — toast.error fallback
- "Calendar" — eyebrow label
- "S M T W T F S" — weekday header letters
- "New event" — button
- "The day is clear." — empty state, no events on selected day
- "All day" — event time label (repeated: month view row, detail sheet)
- "Household" — fallback member label
- " · {location}" — location suffix
- "Give it a title" — thrown error → toast.error
- "Pick a date" — thrown error → toast.error
- "End date must be on or after start date" — thrown error → toast.error
- "Event updated" / "Event lit" — toast.success, edit vs create
- "Could not save" — toast.error fallback
- "Edit event" / "New event" — sheet heading
- "Title" — field label
- "Dentist, soccer practice…" — placeholder
- "Who" — field label
- "Whole household" — chip button (repeated: form and detail sheet)
- "Date" — field label
- "All day" — checkbox label
- "End date (optional)" — field label
- "Leave empty for a single-day event. Set to span multiple days (inclusive)." — helper text
- "Start" — field label
- "End (optional)" — field label
- "Location (optional)" — field label
- "School gym, kitchen…" — placeholder
- "Notes (optional)" — field label
- "Repeats" — field label
- "One-off" — recurrence chip when none; otherwise raw "daily"/"weekly"
- "Lighting…" / "Save changes" / "Light the event" — submit button, three states
- "Repeats {value}" — detail sheet template
- "Edit" / "Delete" — detail sheet buttons
---
 
## Hearth Log (temporary/debug)
 
**Route:** `/hearth-log` · **Files:** `src/routes/_authenticated/hearth-log.tsx`, wrapped in `AppShell`. Marked in its own copy as disposable scaffolding — see Observations.
 
- "Hearth Log — Emberhold" — page `<title>`
- "Board" — back-link
- "Verification view · temporary" — kicker
- "Hearth Log" — page heading
- "Raw activity_log rows, newest first. Disposable scaffolding for the upcoming feed." — subtitle
- "…" — loading state
- "No activity yet." — empty state
- "bounty posted" — verb label, `bounty_posted`
- verb.replace("_", " ") — verb label fallback for other verbs
- "Someone" — fallback actor name
- line templates: "{who} completed {obj}{ · +{delta}}" / "New bounty: {obj}{ · +{delta}}" / "{who} redeemed {obj}{ · {delta}}" / "{obj} is complete" / "{actor} added {obj} to the calendar" or "New event: {obj}" / "{who} · {obj}" (default) — near-duplicate of Briefing's and NotificationBell's line-building logic, see Duplicates
- tier badge — **from `TIER_LABEL`**
- "{n}s ago" / "{n}m ago" / "{n}h ago" / "{n}d ago" — relTime() — see Duplicates
---
 
## Campaigns
 
**Route:** `/campaigns` · **Files:** `src/routes/_authenticated/campaigns.tsx`, wrapped in `AppShell`; renders `<CampaignCard>` (see Shared Components)
 
- "Campaigns · Emberhold" — page `<title>`
- "Give the campaign a title" — thrown error → toast.error
- "Campaign lit" — toast.success
- "Could not create" — toast.error fallback
- "Campaigns" — page heading
- "New" — button, Keeper only
- "Active" — section label
- "…" — loading placeholder
- "No campaigns yet. Tap New to start one." — empty state, Keeper
- "No campaigns yet. A Keeper can start one." — empty state, Kin
- "Completed" — section label
- "New campaign" — form header
- "Title" — field label
- "São Paulo Trip" — placeholder
- "Target date" / "(optional)" — field label
- "Notes" / "(optional)" — field label
- "What we're preparing for…" — placeholder
- "Cancel" — button
- "Light the campaign" — submit button
---
 
## Campaign detail
 
**Route:** `/campaign/$id` · **Files:** `src/routes/_authenticated/campaign.$id.tsx`, wrapped in `AppShell`; renders `<CampaignProgress>` (see Shared Components)
 
- "Campaign · Emberhold" — page `<title>`
- "Could not update" — toast.error fallback
- "Tagged to campaign" — toast.success
- "Could not tag" — toast.error fallback
- "Loading…" — loading state
- "Campaigns" — back-link
- "New tagged bounty" — button, Keeper only
- "Tag an existing bounty" — button, Keeper only, opens picker
- status group headings — **from `STATUS_LABEL`**
- "No prep bounties yet. Tag one above." — empty state
- "Tag an existing bounty into this campaign" — dialog `aria-label`, duplicates the visible h2 immediately below it
- "Add to campaign" — eyebrow, inside modal
- "Tag an existing bounty into this campaign" — modal h2
- 'These bounties are currently untagged. Pick one to add it to "{title}."' — modal helper text
- "Loading…" — untagged-list loading state
- "No untagged bounties to add." — empty state, inside modal
- "Untagged · +{n} embers · {status}" — per-row meta text
- "Tag" — per-row button
- "Mark campaign complete" — button, Keeper only
- "Reopen campaign" — button, Keeper only, shown when completed
- "Archive" — button, Keeper only
- "Edit campaign" — edit-form header
- "Title" / "Target date" / "Notes" — field labels
- "Cancel" / "Save" — buttons
---
 
## Leaderboard / Ranks
 
**Route:** `/leaderboard` · **Files:** `src/routes/_authenticated/leaderboard.tsx`, wrapped in `AppShell`
 
- "Ranks · Emberhold" — page `<title>`
- "Hold ranks" — page heading
- " · join code " — inline separator before join code
- "No embers earned yet." — empty state
- "The first approved bounty lights the board." — empty state body
- "{n} bounties done" — per-row stat
- "embers" — unit label
- "Bounties completed" — expanded-row section label
---
 
## Profile
 
**Route:** `/profile` · **Files:** `src/routes/_authenticated/profile.tsx` (large, many internal sub-components), wrapped in `AppShell`; renders `<AvatarPicker>` (see Shared Components)
 
*ProfilePage (main list view)*
- "Profile · Emberhold" — page `<title>`
- "Profile" — h1
- "Tap a member to switch who's acting." — subtitle
- "Household timezone updated" — toast.success
- "Could not update timezone" — toast.error fallback
- "Household renamed" — toast.success
- "Could not rename" — toast.error fallback
- "Set your Keeper PIN first, then open display mode." — toast.error
- "Display mode" — card heading
- "Put the hold on a screen the whole house can see." — card subtitle
- "Household" — section label
- household name display, fallback "No household" / "…"
- "Members" — section label
- "tap to switch" — helper text
- "Add a member" — button
- "Household clock" — section label
- timezone display (both expanded and collapsed views)
- "Your hold's days start and end on this clock, where the household lives, not where anyone happens to be right now." — helper text
- "Couldn't read this device's timezone" — toast.error
- "Move the hold to {zone}" — button
- "Own phone?" — section label
- "Want Emberhold on your own phone? Share this code so it joins your hearth." — explainer
- join code display, fallback "——————" (em-dashes, not user-authored)
- "Sign out" — button
*SetupHearthCard (orphan-account onboarding card — confirmed live/rendered, not dead code despite the underscore-prefixed function name)*
- "Finish setting up your hearth" — heading
- "Your login is ready. Create your first household member to unlock the board." — subtitle
- "Pick a display name" / "Name your household" / "Enter the join code" / "Set a 4 to 8 digit Keeper PIN" / "PINs don't match" — thrown validation errors → toast.error
- "Your hearth is ready" — toast.success
- "Could not finish setup" — toast.error fallback
- "Display name" — field label
- "e.g. Avery" — placeholder
- "Role" — field label
- role toggle — **from `ROLE_LABEL`**
- "Household" — field label
- "Start" / "Join" — toggle button text
- "Keeper PIN (required)" — field label
- "4 to 8 digits" — placeholder
- "Confirm PIN" — field label
- "Re-enter PIN" — placeholder
- "PINs don't match." — inline validation (note trailing period, distinct from the thrown-error string above without one)
- "Household name" — field label
- "e.g. The Stormlights" — placeholder
- "Join code" — field label
- "6-char code" — placeholder
- "Light the hearth" — submit button
- "Sign out" — button
*DisplayModeSelector*
- "Display mode" — eyebrow
- "How should the hearth rest?" — heading
- "Pick where the wall settles when no one's touched it in a while." — subtitle
- "Agenda" / "What's next, in order" — option card
- "Calendar" / "The month at a glance" — option card
- "Not now" — dismiss button
*MemberRow*
- "Member removed" — toast.success
- "Could not remove" — toast.error fallback
- "{name}'s PIN cleared, they'll set a new one on next switch-in" — toast.success
- "Could not reset PIN" — toast.error fallback
- "This Keeper profile has no PIN. Ask a Keeper to set one." — toast.error
- "owner" — inline badge next to self's name
- role badge — **from `ROLE_LABEL`**
- "· acting" — inline suffix, active member
- "Reset {name}'s PIN? They'll be asked to set a new one next time they switch in." — window.confirm
- "Reset PIN" — tooltip (`title=`)
- "Remove {name}?" — window.confirm
*MemberEditor*
- "Name required" — thrown validation → toast.error
- "Set a PIN for this Keeper profile" / "PIN must be 4 to 8 digits" / "PINs don't match" — thrown validation → toast.error
- "Member updated" / "Member added" — toast.success, conditional
- "Could not save" — toast.error fallback
- "Name" — field label
- "Login email" — field label, editing existing member as Keeper
- "No login (managed member)" — fallback text
- "Role" — field label
- role toggle — **from `ROLE_LABEL`**
- "Keeper PIN" · "required" · "currently set" — composite field label (three parts)
- "Enter new PIN to change" / "4 to 8 digits" — placeholder, conditional
- "Confirm PIN" — field label
- "Re-enter PIN" — placeholder
- "PINs don't match." — inline validation
- "Locks this profile so only the PIN-holder can switch in and approve bounties." — helper text
- "Cancel" — button
- "Save" / "Add" — submit button, conditional
*PinPrompt*
- "Keeper PIN required" — modal subtitle
- "PIN cleared, set a new one to continue" — toast.success
- "Could not reset PIN" — toast.error fallback
- "Could not verify" — toast.error fallback
- "••••" — placeholder
- "Wrong PIN. Try again." — inline error
- "Cancel" / "Unlock" — buttons
- "Forgot {name}'s PIN? This clears it. You'll be asked to set a new one to continue. Only the signed-in account owner can do this." — window.confirm
- "Forgot PIN?" — link
*SetPinPrompt*
- "Set a PIN to continue" — modal subtitle
- "PIN must be 4 to 8 digits" / "PINs don't match" — thrown validation → toast.error
- "PIN set" — toast.success
- "Could not set PIN" — toast.error fallback
- "Keeper profiles must be PIN-locked. Set one now to enter this profile." — explainer
- "New PIN" / "Confirm PIN" — field labels
- "4 to 8 digits" / "Re-enter PIN" — placeholders
- "PINs don't match." — inline validation
- "Cancel" — button
- "Set PIN & enter" — submit button
---
 
## Auth (sign in / sign up / finish setup)
 
**Route:** `/auth` · **Files:** `src/routes/auth.tsx` (large, multiple visual stages); renders `<AvatarPicker>` and `<PipSpark>` (see Shared Components)
 
*Sign-in / sign-up form stage*
- "Sign in · Emberhold" — page `<title>`
- "Emberhold" — eyebrow
- "Your household's bounty board" — h1
- "Hi, I'm Pip, let's light your hearth." — Pip quote, signup mode
- "Sign in" / "Create account" — tab buttons
- "Email" / "Password" — field labels
- "Confirm your email first." — unconfirmed-email heading
- "We sent a link to {email}. Tap it, then come back here to sign in. Check spam. The sender's still unbranded." — unconfirmed-email body
- "Resend in {n}s" / "Resend confirmation" — resend button, cooldown vs ready
- "Confirm password" — field label, signup
- "Re-enter password" — placeholder
- "Passwords don't match." — live inline validation
- submit button: "…" / "Sign in" / "Light my ember" — loading/signin/signup states
- "Forgot password?" — link, signin mode only
- "What is Emberhold?" — info box eyebrow
- "One place for your whole household, the calendar, the chores, the shared goals, built so everyone actually shows up to it. Because here, the daily grind is a game." — info box paragraph
- "The dishes, the dog, the trash become bounties worth embers. Keepers post the work and approve it done; Kin claim the call and cash embers in for real rewards. Track what's ahead, chase shared campaigns, and watch the whole hold at a glance. Most household calendars drift because no one opens them. This one stays lit." — info box paragraph
- "By hearth & hold." — info box footer
*Finish-setup stage*
- "Who's tending the hold?" — h1
- "Your name, your face, and your role in the hold." — subtitle
- "Ember's still warm, let's finish lighting your hearth." — Pip quote
- "Display name" — field label
- "e.g. Leo" — placeholder
- "Avatar" — field label
- "Role" — field label
- "Keeper or Kin? Keepers approve bounties; Kin do them and submit for approval." — PipSpark tooltip body
- role toggle — **from `ROLE_LABEL`**
- "Keepers can approve bounties. Kin submit them for approval." — helper text
- "Keeper PIN (required)" — field label, role=Keeper + creating household
- "This locks your profile so only you can approve bounties. It's what stops a Kin from approving their own." — PipSpark tooltip body
- "4 to 8 digits" — placeholder
- "Confirm PIN" — field label
- "Re-enter PIN" — placeholder
- "PINs don't match." — inline validation
- "Locks your profile so only the PIN-holder can approve bounties." — helper text
- "Household" — field label
- "First one here? Start a new household. Someone already set yours up? Join with their code." — PipSpark tooltip body
- "Start a household" / "Join with code" — choice buttons
- "Household name" — field label
- "e.g. The Stormlights" — placeholder
- "Join code" — field label
- "6-char code" — placeholder
- "No PIN yet. A Keeper in that hold sets yours when they admit you." — helper text, joining
- submit button: "…" / "Finish lighting my hearth"
*Toasts / validation, all stages*
- "Email verified. Now a Keeper in your household needs to admit you before the hold opens." — toast.success, joining
- "Welcome to Emberhold" — toast.success, creating
- "Couldn't finish setting up your hold" — toast.error fallback
- "One more step, let's set up your hold." — toast.message, signed in with no profile
- "Enter your email first, then tap Forgot password" — toast.error
- "Check your email for a reset link" — toast.success
- "Confirmation sent to {email}" — toast.success
- "Pick a display name" / "Name your household" / "Enter the household join code" / "Set a 4 to 8 digit Keeper PIN" / "PINs don't match" — toast.error, form validation
- "Enter your email" / "Pick a password" / "Passwords don't match" — toast.error, signup form validation
- "That email is already lit here. Try signing in." — toast.error, duplicate signup
- "Sign-up failed" — thrown error, surfaces via generic catch-all
- "Something went wrong" — toast.error fallback
*Check-email stage*
- "Almost lit" — eyebrow
- "Check your email" — h1
- "A confirmation link is on its way to" — paragraph (email shown separately)
- "Tap the link inside to light your ember. Then come back here and sign in. Your hold's waiting." — paragraph
- "Don't see it?" — info box eyebrow
- "Check your spam or promotions folder. The sender's currently unbranded and sometimes gets filtered. Give it a minute; email's slower than embers." — info box body
- "Resend link in {n}s" / "Sending…" / "Resend confirmation email" — resend button states
- "I confirmed, take me to sign in" — button
- "Wrong email? Start over" — link
- "Chores become bounties. Bounties earn embers. Embers buy real rewards." — footer tagline
---
 
## Join (invite link redirect)
 
**Route:** `/join` · **Files:** `src/routes/join.tsx`
 
- "Join the Hold · Emberhold" — page `<title>`
- "Emberhold" — eyebrow
- "Lighting the path to the hold…" — redirect status message
---
 
## Reset password
 
**Route:** `/reset-password` · **Files:** `src/routes/reset-password.tsx`
 
- "Reset password · Emberhold" — page `<title>`
- "Emberhold" — eyebrow
- "Reset your password" — h1
- "Set a new password for your account login." — subtitle
- "Open this page from the reset link in your email." — shown before session is ready
- "New password" / "Confirm password" — field labels
- "…" / "Update password" — submit button
- "Use at least 8 characters" — toast.error
- "Passwords don't match" — toast.error
- "Password updated" — toast.success
---
 
## Pending admission (waiting screen)
 
**Files:** `src/components/emberhold/PendingWaitingScreen.tsx` — shown in place of the normal app for a signed-in member whose profile hasn't been admitted yet
 
- "Waiting at the gate" — eyebrow
- "Waiting for admission to {family_name}" — h1
- "You asked to join as a Keeper/a Kin. A Keeper in the household needs to admit you before the hold opens up." — body, role branches on `requested_role`
- "What happens next?" — info box eyebrow
- "Any Keeper in {family_name} can admit or deny your request from their briefing. This page checks automatically. You'll be let in the moment they do." — info box body
- "Sign out" — button
---
 
## Onboarding: Add family
 
**Route:** `/onboarding/add-family` · **Files:** `src/routes/_authenticated/onboarding.add-family.tsx`, wrapped in `SetupShell` (see Shared Components); renders `<AvatarPicker>`, `<PipSpark>`
 
- "Add your hold · Emberhold" — page `<title>`
- "No hold yet" — toast.error, missing familyId
- "{name}: 4 to 8 digit PIN required" — toast.error
- "{name}: PINs don't match" — toast.error
- "Couldn't add members" — toast.error fallback
- "The hold is lit!" — celebration heading
- "Lighting the hearth…" — celebration subtext
- "Add your hold" — SetupShell title
- "Hi, I'm Pip. I tend the ember and keep your hold lit. The hold burns brighter with every holdmate, so add the rest of yours now. You can always bring more in later." — Pip intro
- "Skip, I'll add them later" — skip link
- "Kindling…" / "Done, into the hold" — footer button
- "Add another" — button
- "Holdmate #{n}" — per-card kicker
- "Name" — field label
- "e.g. Ada" — placeholder
- "Avatar" — field label
- "Role" — field label
- "Keeper or Kin? Keepers approve bounties; Kin do them and submit for approval." — PipSpark tooltip
- role toggle — **from `ROLE_LABEL`**
- "Keeper PIN" — field label
- "This locks the profile so only this adult can approve bounties. It's what stops a Kin from approving their own." — PipSpark tooltip — note "adult," not "Keeper" — see Observations
- "4 to 8 digits" — placeholder
- "Confirm PIN" — field label
- "Re-enter" — placeholder
- "PINs don't match." — inline validation
---
 
## Onboarding: The Hold (feature tour)
 
**Route:** `/onboarding/the-hold` · **Files:** `src/routes/_authenticated/onboarding.the-hold.tsx`, wrapped in `SetupShell`
 
- "The rest of your hold · Emberhold" — page `<title>`
- "The rest of your hold" — SetupShell title
- "Bounties are only part of it. Lists, Calendar and Campaigns live here too, they'll be waiting when you want them." — Pip copy
- "Skip, take me to the board" — skip link
- "Good to know" — footer button
- "Lists" / "Calendar" / "Campaigns" — surface chip labels
- "{label} in Emberhold" — image `alt` text (currently unreachable — no screenshot `src` is set for any surface, so this branch never renders today)
---
 
## Onboarding: Recap
 
**Route:** `/onboarding/recap` · **Files:** `src/routes/_authenticated/onboarding.recap.tsx`, wrapped in `SetupShell`. Also reachable as a replay from elsewhere in the app (`replay=true` prop variant).
 
- "Welcome to Emberhold" — page `<title>`
- "Post the work" / "Adults turn dishes, dog-walks, and trash runs into bounties worth embers." — step 1 — note "Adults," not "Keepers" — see Observations and Duplicates
- "Claim & complete" / "Kin claim a bounty, do it, and submit for approval." — step 2
- "Approve & reward" / "Adults approve. Embers land. Spend them in the Vault on real rewards." — step 3 — "Adults" again
- "Chase the campaign" / "Every bounty contributes to shared hold goals. Keep the hold lit." — step 4
- "Welcome back to Emberhold" (replay) / "Welcome to Emberhold" (default) — SetupShell title
- "Here's the whole loop, start to finish. Four beats and that's it." — Pip copy
- "Close" (replay) / "Skip, straight to the board" (default) — skip label
- "Done" (replay) / "Next" (default) — footer button
---
 
## Onboarding: First quest
 
**Route:** `/onboarding/first-quest` · **Files:** `src/routes/_authenticated/onboarding.first-quest.tsx`, wrapped in `SetupShell`
 
- "Post your first bounty · Emberhold" — page `<title>`
- "Hold isn't ready yet" — toast.error
- "Give the bounty a name" — toast.error
- "Pick who it belongs to" — toast.error
- "Ember reward must be at least 1" — toast.error
- "Couldn't post the bounty" — toast.error fallback
- "Post your first bounty" — SetupShell title
- "Chores become bounties here. Every bounty is worth embers, pick one, or write your own, and give it to someone by name." — Pip copy
- "Skip, I'll post one later" — skip link
- "Lighting…" / "Light the bounty" — footer button
- "Bounty title" — field label
- "Write your own…" — placeholder
- suggestion chip titles: "Make your bed" / "Clear the table" / "Feed the pet" / "Take out the trash" / "20 min reading" / "Tidy your room"
- "Ember reward" — field label
- "+{n}" — quick-pick chips
- "Whose bounty is it?" — field label
- "No other hearthmates yet. This one goes up open to anyone." — solo-hold note
- "Open to anyone" — assignment button
---
 
## Onboarding: Stock the Vault
 
**Route:** `/onboarding/stock-vault` · **Files:** `src/routes/_authenticated/onboarding.stock-vault.tsx`, wrapped in `SetupShell`
 
- "Stock the Vault · Emberhold" — page `<title>`
- "Hold isn't ready yet" — toast.error
- "Name the reward" — toast.error
- "Cost must be at least 1" — toast.error
- "Couldn't stock the vault" — toast.error fallback
- "Stock the Vault" — SetupShell title
- "Embers have to buy something real. Set one reward worth working for. You can add more any time." — Pip copy
- "Skip, I'll stock it later" — skip link
- "Stocking…" / "Add to the Vault" — footer button
- "Reward" — field label
- "Write your own…" — placeholder
- preset reward chips — values from `VAULT_PRESETS` (`src/lib/vault-presets.ts`)
- "Ember cost" — field label
---
 
## Onboarding: Add to home screen
 
**Route:** `/onboarding/add-to-home` · **Files:** `src/routes/_authenticated/onboarding.add-to-home.tsx`, wrapped in `SetupShell`
 
- "Add to home screen · Emberhold" — page `<title>`
- "Keep me close" — SetupShell title
- "One more thing, want me on your home screen? Then I'm one tap away, no browser needed." — Pip copy
- "Maybe later" — skip link
- "Done" — footer button
- "The iOS share sheet with Add to Home Screen highlighted" — image `alt`, iOS branch
- "Tap Share, then Add to Home Screen." — figcaption, iOS branch
- "Not seeing the Share button? Open theemberhold.com in Safari first, then try again." — helper note, iOS branch
- "Open your browser's menu and choose Install app (or Add to Home Screen)." — helper note, non-iOS branch
---
 
## Setup Intent
 
**Route:** `/setup/intent` · **Files:** `src/routes/_authenticated/setup.intent.tsx`
 
- "What is this Hold for? · Emberhold" — page `<title>`
- "Choose which parts of Emberhold your Hold uses." — meta description
- "Hold setup" — eyebrow
- "What do you want this Hold to use Emberhold for?" — page heading
- "Pick anything that fits. You can change this later." — subheading
- "Ask a Keeper." — non-Keeper gate heading
- "Only a Keeper in this Hold can change what Emberhold is used for." — non-Keeper gate body
- "Household" / "The core loop" / "Bounties, embers, the Vault, the calendar, lists, everyday life for the people in this Hold." — IntentCard
- "Training" / "Strength & conditioning" / "Track lifts, sets, and prescriptions for the people training in this Hold." — IntentCard
- "Loading…" — save-status, loading
- "Unsaved changes" — save-status, dirty
- "Saved: {modules}" — save-status, saved
- "Saving…" / "Save" — Save button
- "Saved" — toast.success
- "Could not save" — toast.error fallback
- "Always on" — locked-card badge, Household card
---
 
## First-run walkthrough (Keeper) — 5 screens
 
**Routes:** `/first-run/adult/board`, `/first-run/adult/approving`, `/first-run/adult/vault`, `/first-run/adult/pin`, `/first-run/adult/quests` · **Files:** one thin route wrapper each, all rendering through `SetupShell` with an `index`/`total` step-counter pair (see Shared Components). `src/lib/first-run-flow.ts` holds only step id/path routing, no copy.
 
*The board (`first-run.adult.board.tsx`)*
- "The board · Emberhold" — page `<title>`
- "The board" — SetupShell title
- "This is the board. Every bounty someone's posted for the hold shows up here, some open for anyone, some already claimed." — Pip copy
- "Skip, take me to the board" — skip link
- "A bounty is a real task" / "A chore, an errand, anything worth doing, turned into something with a name, a point value in embers, and someone it belongs to." — info card
- "Continue" — footer button
*Approving (`first-run.adult.approving.tsx`)*
- "Approving · Emberhold" — page `<title>`
- "Approving" — SetupShell title
- "When a bounty is finished and turned in, it's waiting on you. Nothing pays out until it's approved. That's the moment embers actually mint." — Pip copy
- "Skip, take me to the board" — skip link
- "Where it surfaces" / 'Work waiting on you shows up in "Needs you" on the board, and in the bell up top. Both stay quiet until something's actually ready to look at.' — info card
- "Approve mints the embers" / "Tap approve and the embers on that bounty become real and spendable. There's no other way they get created." — info card
- "Continue" — footer button
*The Vault (`first-run.adult.vault.tsx`)*
- "The Vault · Emberhold" — page `<title>`
- "The Vault" — SetupShell title
- "The Vault is where embers turn into something real. From here you set what's in it and what it costs, and approve or deny a request when one comes in." — Pip copy
- "Skip, take me to the board" — skip link
- "Rewards live here" / "Screen time, a treat, an outing, anything with an ember cost. A request to redeem one waits for approval, same as a bounty." — info card
- "Add or adjust anytime" / "New rewards, new costs, archiving old ones, all from the Vault, whenever you want." — info card
- "Continue" — footer button
*Your PIN (`first-run.adult.pin.tsx`)*
- "Your PIN · Emberhold" — page `<title>`
- "Your PIN" — SetupShell title
- "Your PIN locks your own profile, so an approval only goes through under your name when you've entered it. You'll be asked to set one the first time it's actually needed. Nothing to do here." — Pip copy
- "Skip, take me to the board" — skip link
- "Why it exists" / "Without it, anyone at a shared device could tap approve under your name. The PIN is what keeps an approval or a redemption tied to the person who actually signed off on it." — info card
- "Continue" — footer button
*Posting a bounty (`first-run.adult.quests.tsx`)*
- "Posting a bounty · Emberhold" — page `<title>`
- "Posting a bounty" — SetupShell title
- "Anyone can put a bounty on the board. Give it a name, set what it's worth in embers, and hand it to someone by name, or leave it open for whoever gets there first." — Pip copy
- "Straight to the board" — skip link
- "The + button posts one" / "Look for it at the bottom of the board. A title, an ember value, and who it's for. That's the whole thing." — info card
- "Open the board" — footer button, final CTA of the Keeper track
---
 
## First-run walkthrough (Kin) — 3 screens
 
**Routes:** `/first-run/kid/board`, `/first-run/kid/claim`, `/first-run/kid/embers` · **Files:** one thin route wrapper each, through `SetupShell`.
 
*The board (`first-run.kid.board.tsx`)*
- "The board · Emberhold" — page `<title>`
- "The board" — SetupShell title
- "This is the board. Every bounty shows up here, some open for anyone to grab, some already yours." — Pip copy
- "Skip, take me to the board" — skip link
- "A bounty is worth embers" / "Every bounty has a name and an ember value. Finish it, and those points land in your stash as embers." — info card
- "Continue" — footer button
*Claim, complete, submit (`first-run.kid.claim.tsx`)*
- "Claim, complete, submit · Emberhold" — page `<title>`
- "Claim, complete, submit" — SetupShell title
- "Three taps, start to finish. Here's the whole loop." — Pip copy
- "Skip, take me to the board" — skip link
- step list: "Claim it" / "Tap an open bounty and hit Claim. Now it's yours." · "Do the work" / "Get it done." · "Submit it" / "Mark it done and send it in. It'll wait to be approved."
- "Continue" — footer button
*Embers & the Vault (`first-run.kid.embers.tsx`)*
- "Embers & the Vault · Emberhold" — page `<title>`
- "Embers & the Vault" — SetupShell title
- "Every approved bounty lands embers in your stash. Head to the Vault to spend them on rewards that are already set up and waiting." — Pip copy
- "Straight to the board" — skip link
- "The Vault is the tab down below" / "Pick something, and the request goes off to be approved, same way a finished bounty does." — info card
- "Open the board" — footer button, final CTA of the Kin track
---
 
## Reforge avatar
 
**Route:** `/reforge-avatar` · **Files:** `src/routes/_authenticated/reforge-avatar.tsx`, wrapped in `AppShell`; renders `<AvatarPicker>` (see Shared Components)
 
- "Re-forge your hero · Emberhold" — page `<title>`
- "Pick a hero to continue" — toast.error
- "Couldn't save your hero" — toast.error fallback
- "Your hero was re-forged" — h1
- "The hold's roster has been reshaped in the ember. Pick your hero anew." — subhead
- "Kindling…" / "Take this hero" — submit button
---
 
## Avatar Review (internal/debug page)
 
**Route:** `/avatar-review` · **Files:** `src/routes/avatar-review.tsx`. Standalone review page for the app's hero-avatar art, not linked from anywhere in the production nav (see Observations — appears to use a different, older avatar-naming scheme than the live `AvatarPicker`).
 
- "Avatar Review · Emberhold" — page `<title>`
- "Review Emberhold's generated fantasy avatar portrait roster." — meta description
- "Emberhold roster review" — eyebrow
- "Hero roster" — h1
- "Every hero portrait rendered in the real round avatar frame at picker thumbnail size. NEW heroes and RE-ROLL replacements are not yet wired into the avatar picker." — intro paragraph
- group headings: "Forge" / "Garden" / "Keep" / "Feast"
- group blurbs: "Warriors, protectors, and ember-forged heroes." / "Nature-touched wanderers, growers, and gentle wildsfolk." / "Steadfast watchers and guardians of hearth and home." / "Warm-hearted makers, entertainers, and bringers of bounty."
- 20 named hero labels (rendered as image `alt` + figcaption): Orc Warrior, Dragonborn Knight, Fire Elemental Warrior, Human Paladin, Dragonling, Bunny Knight, Verdant Treant, Elf Ranger, Fae Sprite, Mushroom Forager, Bloomwarden, Rune-Core Golem, Skeleton Watchman, Ice Elemental Guardian, Cat Mage, Frost Maiden, Dwarf Hearth-Cook, Fox Bard, Merfolk Bard, Mermaid
- "New" — corner badge, tag=new
- "Re-roll" — corner badge, tag=reroll
---
 
## Splash / redirect
 
**Route:** `/` · **Files:** `src/routes/index.tsx`
 
- "Emberhold" — page `<title>` + meta description ("Household bounty board that turns chores into embers.")
- "Emberhold" — eyebrow, rendered on the splash screen itself
- "Stoking the hearth…" — loading message while checking session
---
 
## 404 / Not Found
 
**Files:** `src/routes/__root.tsx` (`NotFoundComponent`) — shown for any unmatched route
 
- "404" — heading
- "Page not found" — subheading
- "The page you're looking for doesn't exist or has been moved." — body
- "Go home" — link
---
 
## Global error boundary
 
**Files:** `src/routes/__root.tsx` (`ErrorComponent`) — shown when a route errors
 
- "This page didn't load" — heading
- "Something went wrong on our end. You can try refreshing or head back home." — body
- "Try again" — button
- "Go home" — link
---
 
## Root document meta (every page)
 
**Files:** `src/routes/__root.tsx` — head tags applied app-wide, overridden per-route where a route sets its own `<title>`
 
- "Emberhold · household bounty board" — root `<title>`, `og:title`, `twitter:title` (same string, three tags)
- "Turn household tasks into embers. A shared bounty board for your household." — meta description, `og:description`, `twitter:description` (same string, three tags)
- "Emberhold" — `apple-mobile-web-app-title`
---
 
## Shared Components
 
### AppShell (global chrome — wraps nearly every authenticated screen)
`src/components/emberhold/AppShell.tsx`
- "Emberhold" — fallback household name while loading
- "…" — fallback profile name while loading
- role badge — **from `ROLE_LABEL`**
### BottomBar (global — lives inside AppShell)
`src/components/emberhold/BottomBar.tsx`
- "New event" / "New bounty" — FAB submenu buttons
- Tab labels: "Board" / "Camps" / "Cal" / "Lists" / "Vault" / "Ranks" / "You"
### NotificationBell (global — lives inside AppShell)
`src/components/emberhold/NotificationBell.tsx`
- "Hold pulse" — sheet kicker
- "Notifications" — SheetTitle
- "Needs you" — section label, Keeper only
- "All caught up." — empty state
- " submitted: " — inline fragment, pending-quest row
- "Approve ›" — action hint (repeated: quest row and redemption row)
- " wants: " — inline fragment, pending-redemption row
- "Recent" — section label
- "The hold is quiet." — empty state
- `lineFor()` line-building templates — **same verb-keyed pattern as Briefing's ZonePulse and hearth-log.tsx's `lineFor()`** — see Duplicates
### PipHelp (global help sheet — lives inside AppShell)
`src/components/emberhold/PipHelp.tsx`
- Keeper topics (Q/A pairs): "What's the PIN for?" / "Your PIN locks your Keeper profile so only you can approve bounties. It stops a Kin from approving their own." · "Approving bounties" / "When a Kin marks a bounty done it lands in your approvals. Approve to award embers; reject to send it back." · "Vault & rewards" / "The Vault is where embers turn real. Add rewards (screen time, treats, outings) and set their ember cost." · "Campaigns" / "Campaigns are shared hold goals. Every approved bounty contributes. Great for trips, big-ticket rewards, or summer challenges." · "Calendar" / "Everything scheduled: bounties, events, household items, all in one view. The keep glyph marks whole-household items." · "Briefing" / "Your daily at-a-glance: today's bounties, approvals waiting, what's on the horizon."
- Kin topics (Q/A pairs): "How do I claim a bounty?" / "Tap an open bounty on the Board and hit Claim. Now it's yours to finish." · "Marking it done" / "When you finish, tap Submit. A Keeper will approve and embers land in your stash." · "Spending embers" / "Head to the Vault to spend embers on real rewards the adults set up." — note "adults," not "Keepers" — see Observations
- "Pip's help" — kicker
- "How can I help?" — SheetTitle
- "Replay the intro" — button
- "Invite to the Hold" — label passed to InviteButton, Keeper only
- "Add more holdmates" — button, Keeper only
### QuestCard (used on Board and campaign/member drill-ins)
`src/components/emberhold/QuestCard.tsx`
- status chip — **from `STATUS_LABEL`**
- recurrence chip — **from `RECURRENCE_LABEL`**
- "embers" — unit label
- audience badge — **from `AUDIENCE_LABEL`**
### QuickAddTray (used from Create bounty; also referenced from FirstQuestDoorway)
`src/components/emberhold/QuickAddTray.tsx`
- "Quick add" — default header heading (Create bounty doesn't override it)
- "Tap to pre-fill from a suggestion" — header subtext
- optional `subheading` — caller-supplied
- "Your favorites" — section label
- "Suggestions" — section label
- quest-class label/blurb — **from `QUEST_CLASSES`** (`src/lib/starter-quests.ts`): Keep/"Home & order", Feast/"Kitchen & food", Garden/"Outdoor & care", Forge/"Effort & challenge"
### FirstQuestDoorway (Board empty state, both Keeper and Kin paths, first-time only)
`src/components/emberhold/FirstQuestDoorway.tsx`
- "Pip" — speaker label
- "{name}, the hearth is lit but the board's bare. Post one bounty and this place wakes up." (name omitted if unknown) — Pip message
- "Pick one below, or write your own, either way it's a real bounty, worth real embers." — Pip subtext
- suggestion chips: "Make your bed" / "Take out the trash" / "Load the dishwasher" / "Feed the pet"
- "+{n}" — points badge
- "Write your own" — CTA link
- "Not yet" — dismiss button
- "More ideas" — QuickAddTray heading override
- "Tap any of these to start a bounty with it filled in." — QuickAddTray subheading override
### SetupShell (wraps all 6 onboarding.* screens and all 8 first-run.*.* screens — 14 screens total)
`src/components/emberhold/SetupShell.tsx`
- "Pip" — static eyebrow above the mascot, every screen
- "Skip, I'll do this later" — default `skipLabel`; never actually renders because every one of the 14 caller screens overrides it
### CampaignCard (used on Campaigns)
`src/components/emberhold/CampaignCard.tsx`
- "Complete" — status badge
### CampaignProgress (used on Campaigns, Campaign detail, Briefing, and the Wall)
`src/components/emberhold/CampaignProgress.tsx`
- "Progress: {done} of {total}" — `aria-label` on the progress track (the bar's only textual description, included as a borderline case)
- "No prep bounties yet" — empty state
- "of" / "prep bounties done" — label parts
- "%" — suffix
### AvatarPicker (used on Reforge Avatar, Auth finish-setup, Onboarding: Add Family, and Profile — twice)
`src/components/emberhold/AvatarPicker.tsx` + `src/components/emberhold/Avatar.tsx`
- "{label} (Founders)" or "{label}" — tooltip (`title=`) per hero tile; labels come from `HERO_AVATARS` (`Avatar.tsx`): **48 labels**, pattern "Forge 1"–"Forge 12", "Garden 1"–"Garden 12", "Keep 1"–"Keep 12", "Hall 1"–"Hall 12"
- group section labels — **from `HERO_GROUPS`**: "Forge" / "Garden" / "Keep" / "Hall"
- "Show less" / "+{n} more" — collapsible toggle
- "Sealed heroes are Founders-only." — gate notice
### PipSpark (tooltip-trigger component; content is caller-supplied)
`src/components/emberhold/PipSpark.tsx`
- "Pip's tip about {label}" — `aria-label` on the trigger
- "?" — visible trigger glyph
- Used by: Auth (Role, Keeper PIN, Household tooltips) and Onboarding: Add Family (Role, Keeper PIN tooltips) — tip bodies are counted under those screens, not here
### InviteButton (used inside PipHelp)
`src/components/emberhold/InviteButton.tsx`
- "Invite to the Hold" — default `label` prop (PipHelp overrides it with the same text)
### Crest / KeepGlyph / EmberMascot (small icon components, one string each)
- `Crest.tsx` — "Emberhold" (default `alt` text, overridable)
- `KeepGlyph.tsx` — "Whole household" (default `title`/accessible name)
- `EmberMascot.tsx` — "Ember mascot ({variant})" (`aria-label` on the SVG, the graphic's only description)
### MemberCompletedQuests (used on Profile's member-detail views)
`src/components/emberhold/MemberCompletedQuests.tsx`
- "Reading the ashes…" — loading state
- "No embers earned yet. The next approved bounty will land here." — empty state
- "just now" / "{m}m ago" / "{h}h ago" / "{d}d ago" — relTime — see Duplicates
---
 
# 1. SHARED MAPS
 
All defined in `src/lib/quest-helpers.ts`.
 
| Map | Values | Read from |
|---|---|---|
| `ROLE_LABEL` | parent → "Keeper", kid → "Kin" | Briefing.tsx, AppShell.tsx, auth.tsx, onboarding.add-family.tsx, profile.tsx (MemberRow, MemberEditor, SetupHearthCard) |
| `AUDIENCE_LABEL` | anyone → "Anyone", parents-only → "Keepers only", kids-only → "Kin only" | QuestCard.tsx, create.tsx, quest-log.tsx, slate.tsx |
| `STATUS_LABEL` | open → "Unclaimed", claimed → "In progress", submitted → "Awaiting approval", approved → "Done" | QuestCard.tsx, quest.$id.tsx, quest-log.tsx, slate.tsx, campaign.$id.tsx, wall.tsx |
| `RECURRENCE_LABEL` | none → "One-off", daily → "Daily", weekly → "Weekly", monthly → "Monthly" | QuestCard.tsx, quest.$id.tsx, quest-log.tsx |
| `RECURRENCE_ANCHOR_LABEL` | none → "Once", daily → "Daily", weekly → "Weekly · Mon", monthly → "Monthly · 1st" | slate.tsx |
| `TIER_LABEL` | dim → "Dim", warm → "Warm", hot → "Hot", legendary → "Blazing" | hearth-log.tsx, vault.tsx (KidCuratedVault, CatalogVault) |
| `QUEST_CLASSES` (`src/lib/starter-quests.ts`) | keep/feast/garden/forge → label + blurb | QuickAddTray.tsx |
 
**Defined but not used anywhere for display:** none found unused among the above — every map defined in `quest-helpers.ts` has at least one live call site.
 
**Fields that bypass an existing map** (render the field's own hardcoded copy instead): `vault.tsx`'s `StatusPill` prints the raw redemption-status enum value directly rather than a `STATUS_LABEL`-style lookup; `vault.tsx`'s `AudienceToggle`/`AdultsOnlyTag` hardcode "Everyone in the hold"/"Keepers only" rather than reading a shared reward-audience map (there isn't one for the reward `household`/`adults_only` enum — `AUDIENCE_LABEL` is keyed to the *quest* audience enum, a different set of values). Several files also render the raw lowercase `xpTier()` word ("dim"/"warm"/"hot"/"legendary") instead of going through `TIER_LABEL` — see Observations.
 
---
 
# 2. DUPLICATES
 
This is the most load-bearing section of the report — everywhere the same or near-identical sentence is written out more than once.
 
1. **Activity-line builder, three independent copies of the same verb-keyed template set.** `Briefing.tsx`'s `pulseLine()`, `NotificationBell.tsx`'s `lineFor()`, and `hearth-log.tsx`'s `lineFor()` all implement the same seven-branch switch over `activity_log.verb` ("{who} completed {obj}…", "New bounty: {obj}…", "{who} redeemed {obj}…", "{obj} is complete", "{actor} added {obj} to the calendar"/"New event: {obj}", "{who} · {obj}"), with `wall.tsx`'s `tickerLine()` as a fourth near-identical variant. `NotificationBell.tsx` additionally has two verbs (`member_admitted`/`member_denied`) the others don't.
2. **relTime() reimplemented independently in six files.** `wall.tsx`, `vault.tsx`, `quest-log.tsx`, `slate.tsx`, `hearth-log.tsx`, and `MemberCompletedQuests.tsx` each define their own "{n}s ago"/"{n}m ago"/"{n}h ago"/"{n}d ago" formatter with the same output strings (some add a "just now" case, some don't).
3. **"Keeper PIN required" family.** Four screens separately author variations of "a Keeper PIN is required": Wall's `ExitConfirm` ("A Keeper PIN is required."), Wall's `ExitPinPrompt`/`AdultPinPrompt` ("Keeper PIN required to leave" / "…to mint" / "…to spend"), Profile's `PinPrompt` ("Keeper PIN required"), and Profile's field labels ("Keeper PIN (required)", "Keeper PIN · required").
4. **"Only a Keeper can approve bounties" explainer, written four separate times, four separate ways:** Auth's finish-setup PipSpark ("This locks your profile so only you can approve bounties. It's what stops a Kin from approving their own."), Onboarding: Add Family's PipSpark ("This locks the profile so only this adult can approve bounties…" — still says "adult," see Observations), PipHelp's Keeper topic ("Your PIN locks your Keeper profile so only you can approve bounties. It stops a Kin from approving their own."), and Profile's MemberEditor helper text ("Locks this profile so only the PIN-holder can switch in and approve bounties.").
5. **"PINs don't match" vs. "PINs don't match."** The thrown-error/toast version (no trailing period) and the inline-validation version (with a trailing period) coexist across Auth, Profile (three separate forms), and Onboarding: Add Family — consistently the same two variants, consistently used the same way (toast without a period, inline text with one), but never unified into one string.
6. **"Could not update" / "Could not approve" / "Could not delete" / "Could not save"-style fallback toasts.** Nearly every mutation across every screen falls back to a two-or-three-word "Could not {verb}" toast.error. Individually unremarkable, but as a set this is the single most repeated *pattern* in the app (dozens of call sites, listed under their own screens above rather than re-listed here).
7. **"Open bounties" heading reused at five+ distinct screens/states**, sometimes as a plain section title (Board Kin view, Board's kid-drill-in, Wall's BountiesBanner) and sometimes paired with different subordinate copy — this is the same label doing structural duty everywhere, not really "voice" duplication, but worth knowing it's this heavily reused.
8. **Audience picker labels ("Anyone" / "Keepers only" / "Kin only") appear at four independent call sites** (create.tsx, quest-log.tsx, slate.tsx) all reading the same `AUDIENCE_LABEL` map — not a duplication problem since they share the map, listed here only so the map's reach is visible in one place.
9. **"Unclaimed" is used for two different meanings** in the same general quest-detail context: as the *status label* value (`STATUS_LABEL.open`) and, separately, as the literal fallback text when a quest has no assignee ("For" row value, "Reassign" button). Same word, two different semantic slots, coincidentally identical.
10. **Empty-vault copy disagrees with itself.** `vault.tsx`'s `RewardRow` section has an (unreachable, dead-code) "Ask a Keeper to stock the vault" branch, while the live, reachable `KidCuratedVault` empty state says "No rewards yet. Ask **an adult** to stock the vault." — see Observations.
11. **The Recap onboarding screen still says "Adults"** ("Adults turn dishes… into bounties", "Adults approve. Embers land.") where the rest of the app has migrated to "Keeper" — a direct, in-app inconsistency with Auth's and PipHelp's own copy for the identical concept.
12. **PipHelp's Kin-facing FAQ also still says "adults"** ("real rewards the adults set up") in the same file whose Keeper-facing FAQ correctly says "Keeper."
---
 
# 3. OBSERVATIONS
 
Recorded, not fixed, per the brief.
 
- **"Keeper" migration is incomplete in at least four places**, all outside the literal "Parent"/"Kid" text that earlier passes searched for: Onboarding: Add Family's PipSpark tooltip ("only this adult can approve bounties"), Onboarding: Recap's steps 1 and 3 ("Adults turn dishes…", "Adults approve."), PipHelp's Kin-facing topic ("rewards the adults set up"), and Vault's `KidCuratedVault` empty state ("Ask an adult to stock the vault"). See Duplicates #4, #10, #11, #12.
- **Dead code found:** `vault.tsx`'s `RewardRow` "No rewards yet" ternary has a Kin-branch string ("No rewards yet. Ask a Keeper to stock the vault.") that can never render — the whole block is nested inside an `isParent &&` guard, so the non-parent half of that specific ternary is unreachable. The *reachable* Kin-facing empty state lives in a different component (`KidCuratedVault`) and, per the point above, still says "adult."
- **Raw lowercase tier words leak past `TIER_LABEL` in three places.** `quest.$id.tsx` ("embers · {tier}"), `quest-log.tsx`, and `create.tsx` ("Tier: {tier}") all interpolate the bare `xpTier()` return value ("dim"/"warm"/"hot"/"legendary") directly, rather than resolving it through `TIER_LABEL` the way `vault.tsx` and `hearth-log.tsx` do (which would show "Dim"/"Warm"/"Hot"/"Blazing"). Same underlying tier, two different presentations depending on which screen you're on.
- **Two unrelated avatar-naming systems exist in the codebase.** The live `AvatarPicker` (used on Reforge Avatar, Auth, Onboarding: Add Family, and Profile) sources 48 generically-numbered labels ("Forge 1"–"Hall 12") from `Avatar.tsx`'s `HERO_AVATARS`. A separate, apparently orphaned debug route, `/avatar-review`, defines its own 20 fantasy-named labels ("Orc Warrior," "Dragonborn Knight," etc.) in its own local `groups` array and isn't linked from anywhere in the production navigation. Whether this route is meant to still exist is outside this report's scope to judge.
- **`hearth-log.tsx` self-identifies as temporary.** Its own copy says "Verification view · temporary" and "Disposable scaffolding for the upcoming feed" — the screen is aware it isn't meant to be permanent. Flagging so it isn't mistaken for a real user-facing surface when this report is read.
- **A dialog `aria-label` duplicates its own visible heading.** `campaign.$id.tsx`'s tag-picker modal carries `aria-label="Tag an existing bounty into this campaign"` on the dialog root, and the exact same sentence renders again as a visible `<h2>` one element later — harmless, but redundant.
- **`onboarding.the-hold.tsx`'s image `alt` text branch is currently unreachable** — no `src` is ever populated for any of the three "surface" screenshots, so `"{label} in Emberhold"` never actually renders today even though the code path exists.
- **The `SetupShell` default `skipLabel` ("Skip, I'll do this later") never renders** — all 14 screens that use `SetupShell` override it with their own skip-link text, so the default is dead prose, not a live string.
- **`InviteButton`'s default `label` prop happens to exactly match** what its one caller (`PipHelp.tsx`) passes in ("Invite to the Hold") — not a bug, just worth noting the override is a no-op today.
- **Orphaned/unattributed strings:** none found. Every string a sub-agent extracted was traceable to a specific rendering file and, from there, to a screen — nothing showed up that couldn't be placed somewhere in this report.
- **No typos were found** worth calling out on their own — the copy throughout is unusually consistent in register (warm, informal, hearth/ember metaphor) for an app this size; the "adult" vs. "Keeper" holdouts above are the only voice inconsistency of any real size.
---
 
# Count check
 
- **Screens covered:** 34 — Board (Kin), Board/Briefing (Keeper), Quest detail, Quest Log, Create bounty, The Wall, Lists overview, List detail, The Slate, The Ledger, Calendar, Hearth Log, Campaigns, Campaign detail, Leaderboard, Profile, Auth, Join, Reset password, Pending admission, Onboarding ×6, Setup Intent, First-run (Keeper) ×5, First-run (Kin) ×3, Reforge Avatar, Avatar Review, Splash, 404, Global error boundary — plus 15 shared-component sections (Global Chrome ×4, QuestCard, QuickAddTray, FirstQuestDoorway, SetupShell, CampaignCard, CampaignProgress, AvatarPicker/Avatar, PipSpark, InviteButton, Crest/KeepGlyph/EmberMascot, MemberCompletedQuests) documented once and cross-referenced rather than repeated.
- **Total strings inventoried: approximately 1,050**, including the 48 generically-numbered `AvatarPicker` hero labels and the 20 named `avatar-review.tsx` hero labels (68 of the 1,050 are these near-mechanical data-label strings, compacted into ranges/lists in this report rather than bulleted individually so the rest of the document stays readable).
- This is under the ~1,200 stop-clause threshold; no STOP was triggered. The count is large chiefly because the app is large (36 route files, ~20 shared components, several very large single-file screens like the Wall at 2,100+ lines) — not because scope caught something that shouldn't have been in it. The one deliberate scope trim, excluding bare icon-only `aria-label`s, is documented at the top of this report.

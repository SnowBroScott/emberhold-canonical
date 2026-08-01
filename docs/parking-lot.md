# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

**THE SCREEN COPY PASS** is its own running section below. It is a review inventory, not a backlog item — it tracks which screens have been read for voice and which haven't.

---

## Inbox (untriaged)

*(empty — triaged 2026-08-01)*

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**The rule, established 2026-07-31: DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**The second rule, established 2026-08-01: NO EM DASHES IN USER-FACING COPY — and none in the briefs handed to Lovable, because the second is how the first happens.** 125 removed on 08-01 across 43 files. ⚠️ **En dashes in time and date ranges are typographic convention, not the tell. Bare `—` used as a "nothing set" placeholder glyph is a display value, not prose. Both stay.**

**The disease it treats:** design-rationale phrasing from jAIne's build briefs gets rendered by Lovable as UI copy. The app ends up explaining its own architecture to a nine-year-old. *"Everything with a future," "recurring duties — one line, forever," "here until they're approved"* were all brief text, verbatim, on the glass. **Punctuation escapes the same way the reasoning does.**

**The comparison point for what GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to an adult"* and the wall's *"AN ADULT APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the approval gate, which is invisible and is the thing people get wrong.

**And what good VOICE looks like, from 08-01:** *"Clear skies ahead."* beat the flat *"Nothing scheduled."* because the eyebrow above it already reads ON THE HORIZON — the line extends an existing metaphor instead of inventing one. *"Nothing on the fire for this day"* was the failure mode of the same instinct: reaching for voice on a line that only needed to state a fact.

**Batch before firing.** Four strings is not worth a Code job on its own; accumulate a few screens.

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31** | Eyebrow → EVERYTHING THAT'S LIVE. Standing + One-offs subtitles deleted. ⚠️ **The standing-duties blurb needs a SECOND look post-em-dash-sweep — the dashes were structural and the commas sag. Deletion is likelier than repunctuation.** |
| **The Ledger** | ✅ **Reviewed 07-31** | Own copy already clean; inherits the Slate's link. |
| **Auth / sign-in** | ✅ **Reviewed 08-01** | Subhead deleted. WHAT IS EMBERHOLD block kept — it earns its place and says the same thing better. |
| **Campaigns** | ✅ **Reviewed 08-01** | Description deleted entirely. The tab is labeled, the bounties are grouped on screen, the bar is visibly filling. |
| **Calendar** | ✅ **Reviewed 08-01** | Day empty state → **"The day is clear."** Date-agnostic by design; the header above names the day. |
| **Briefing / Hub** | ✅ **Reviewed 08-01** | Horizon empty state → **"Clear skies ahead."** ⚠️ *The FAB overlapping the Campaigns progress bar is layout, not copy.* |
| Board | ⬜ unreviewed | |
| Vault | ⬜ unreviewed | ⚠️ *"Each request goes to an adult"* is a KEEP — the model for good subtitle copy. |
| Lists | ⬜ unreviewed | ⚠️ The "5 OPEN · 348 DONE" fossil counter is a separate defect, not copy. |
| Ranks / leaderboard | ⬜ unreviewed | |
| You / profile | ⬜ unreviewed | |
| Create / edit | ⬜ unreviewed | |
| Bounty detail (`quest.$id`) | ⬜ unreviewed | |
| The wall | ⬜ unreviewed | ⚠️ The only semi-public surface. Ranks above the private screens. ⚠️ **It holds independent copies of strings that also live in `Briefing.tsx` — read them as a pair.** |
| First run — adult (6 screens) | ⬜ unreviewed | ⚠️ Flagged 07-30 as deserving a second read now that it's live and renamed. **Heavily rewritten by the em-dash sweep; worth a voice read.** |
| First run — kid / joiner (3 screens) | ⬜ unreviewed | |
| Onboarding — first bounty | ⬜ unreviewed | ⚠️ Was completely unswept until 07-31. High-stakes screen; read it whole. |
| Notifications | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | **Deliberate debug surface, kept until Gate C.** Unswept on purpose so it stays obvious which surface is which. |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller.** The column does two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels. **(b) is jAIne's lean — the same shape as the marker's `_profile_id` fix, which is now the third shipped instance of that pattern.** Design call, not a bug fix.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.** ⚠️ **`useActiveMember().role` reads the switched-to profile client-side, while every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is.** **Three RPCs now take a validated actor id specifically to bridge that gap. A fourth would be a pattern; a tenth would be an architecture.**
- **🟠 SHOULD `logActivity` MOVE SERVER-SIDE?** It is a client-side call bolted onto each call site by hand, so every new site can forget — and one did, silently, for the entire life of the wall's approve path. **The mechanism fix is the definer RPC writing its own log row.** Cost: a migration, and the RPC has to know the display label. **Against: three surfaces render their own lines from the verb enum and 08-01 confirmed they are identical, so the label may not belong in the DB at all.**
- **🟠 IS `parent_self_redeem` SKIPPING THE APPROVAL GATE CORRECT?** It inserts `status='approved'` with `decided_by = auth.uid()` in one call. An adult redeeming their own embers is requester and approver simultaneously. **Reads as deliberate in the code and has never been written down.** Ratify it or change it, but stop having it be an undocumented behavior of the economy.
- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** **jAIne's lean: do NOT auto-default and do NOT guess.** The roster is the one place a hearthmate says "this is me." What's missing is a **nudge** — make the empty seat tappable → routes to the picker. **The one exception is the wall**, where a silhouette is a dead spot on an ambient display. **Raised 2026-07-29, never ratified.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** Creation stays ungated — **what is unsettled is which surface is wrong.**
- **WHERE DOES THE REST-TIMER ALERT FIRE — the garage screen, or the pocket?** **jAIne's lean: the screen owns the SESSION; phones own the TIMER.** Taste call in a room jAIne can't see. **Scott's.**
- **In the agenda lane, does the rest timer SHARE the cell with load/weight/reps or REPLACE it?** Scott's sketch has it replace. **jAIne flagged the objection, never ratified.**
- **Does the LLM plan BOTH sessions in one call, or one call per person?** **The contention feature effectively requires one call — confirm that's intended before it becomes an implicit constraint.**
- **THE FORGE'S SHAPE — Option A vs Option B, inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** Prescription-first, pre-session gate, catalog-backed substitution, contention resolution, reason string, log, rest timer, display mode. **The bar is a USER TEST: Scott and May stop opening Fitbod.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence. No fungible currency.
  - **What B owes:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? (3) does a collection survive the calibration check?
  - **The standing risk:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. **A one-time SKU funds a one-time year.** **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Module navigation.** Seven tabs is one past mobile comfort. ✅ **The Slate/Ledger half is ANSWERED and shipped.** **Forge's contained-route posture still defers the module half.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.**
- **⚠️ Backend ownership + data backup.** Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 `master-spec.md` OWES A NAMED FOLD — FOUR REGIONS, FIFTEEN MINUTES.** Line 8 (the `verbLabel` open question, now answered). Lines 298–299 (the marker's read/write ⚠️, now shipped). Line 482 (`/quest-log` + `/hearth-log` as "deletion is a follow-up," now reclassified as a kept debug surface until Gate C). Line 753 (the copy-discipline section, needs the em-dash rule). ⚠️ **jAIne named these from a grep, not a read. Do the fold from a read.** **Open the next session with it.**
- **🟠 PAGE TITLES USE A COLON AND SHOULD NOT.** `Board: Emberhold` across 32 route files plus `__root.tsx`'s title/og:title/twitter:title. **A colon implies containment; the convention for a tab title is a pipe or a middot — `Board · Emberhold`.** The sweep picked a form jAIne never specified. **Free Code, mechanical, do it before it lands in link previews.**
- **🟠 DELETE THE THROWAWAY TEST BOUNTY.** "Do Not Approve Testing Roll-Over." Purpose served, never approved, Delete available. **One tap.**
- **🟠 `slate.tsx`'s STANDING-DUTIES BLURB — SECOND LOOK.** The em dashes were structural, not decorative; commas replaced them and it sags. ⚠️ **Deletion is likelier right than repunctuation — the Slate is showing standing duties while the copy explains what standing duties are.** **Scott's eyes.**
- **🖊️ THE SCREEN COPY PASS — see the section above.** Slate ✅ Ledger ✅ Auth ✅ Campaigns ✅ Calendar ✅ Briefing ✅.
- **Walk the kid JOINER flow with an actual kid.** ✅ **Unblocked 08-01** — the marker was the dependency. First-run fires and completes correctly on a profile switch; **the join path itself has still never been exercised.**
- **`member_admitted` renders as `"Mom · Leo"` — NOW SCOPED.** The `activity_verb` enum has seven values; three renderers name five. **Two missing cases in three identical switches, not a mystery.**
- **`Testing retired` stays retired** once its successor's date arrives. One look.
- **The Slate detail panel read IN PROGRESS while the row header read "Done today."** Panel and header may render off different instances. Noticed 07-31, not chased.
- **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`** — a failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- **Deleting a bounty may orphan its calendar event.** Creating one writes an `EVENT CREATED` row; the deleted `Testing retired` was never checked against the calendar. **One look.**
- **Recurrence chip consistency.** Reads `Monthly · 1st` on the Slate. **Confirm the board and create/edit agree.**
- **Two derivations of role** — `profiles.role` vs `user_roles` in `useMyProfile()`. ⚠️ **And a third split: `useActiveMember().role` vs `has_role(auth.uid())`.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building.**
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- **The Briefing's FAB overlaps the Campaigns progress bar.** Covers the middle of the São Paulo Trip bar. The board escapes it because a card gap sits there. **Layout, Scott's eye.**
- **Prod test-object cleanup.** `Testing redemption tracking` in the Ledger; `Testing retired` in the Slate's Retired section, **and canon records no un-retire affordance.**
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. **Now critical path #1.**
- **Grant-revoke verification probe job.** Drafted, deferred eight times.
- **The floor — avatar render fallback.** **Check the wall.**
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

---

## NEXT (soon — off the critical path)

### The rename's tail — CLOSED

- ✅ **`Quest`→`Bounty` IS LANDED.** The 07-31 coverage grep found `onboarding.first-quest.tsx` completely unswept (eight strings) plus two single misses, fixed all ten, and confirmed `public/`, the PWA manifest and every shipped `.json`/`.md` were already clean.
- ✅ **THE ENUM-LEAK RESIDUE IS CLOSED TOO, WITHOUT A FIX.** 08-01 recon proved `hearth-log.tsx`'s `verbLabel()` is the **only** place in the codebase that derives display text from a raw verb. `wall.tsx`, `Briefing.tsx` and `NotificationBell.tsx` are byte-identical switches whose `default` never touches `row.verb`. **`QUEST APPROVED` is reachable on one unlinked debug page and nowhere else. Accepted.**
- **The Haiku vocabulary sweep has one target left:** `Feast`→`Hall`, and user-facing `points`→embers. **Check whether other `points` instances survived.**

### Onboarding, phase three

- **Walk the kid joiner flow with an actual kid.** ✅ **Unblocked.** Three beats to the adult's five, built and published, join path never exercised.
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read** now that it's live, renamed, and heavily repunctuated. ⚠️ **Part of the screen copy pass.**
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Toolchain

- **⚠️ `routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** The generated and committed files disagree, every build surfaces it, and every agent has to know to throw it away.
- **`progression.test.ts` has a pre-existing `tsc --noEmit` error** — missing `vitest` types. **Surfaced on all six Code jobs across 07-31 and 08-01.** Clean it with the `progression.ts` job.
- **Claude Code's "sync before reading" rule needs a forcing function.** ✅ **Worked six times.** **Keep the line in every Code brief.**
- **`package-lock.json` and `query_quest.mjs` are sitting untracked in the working tree.** The real lockfile is `bun.lock`.
- **`wall_request_redemption` IS CALLED FROM THE VAULT AND ITS NAME LIES.** Deliberate debt — renaming an RPC is a migration. **Rename when something else takes that function to Lovable.**
- **⚠️ PLAN-MODE ITERATIONS ARE BILLED.** Two rounds cost two credits on 08-01. **Plan mode stays the right call for migrations — a code revert is not a database revert — but the review is ONE pass.**

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for V1.** Option A's shape lives in `master-spec.md` Part II — this section holds build order only.*

1. **The catalog** — a MAKE job. Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
2. **`progression.ts`** — no progression axis except load. **Pure TypeScript, ten tests, zero Supabase imports, zero credits.**
3. **Pre-session constraint acceptance** — scope with the rep-compensation job.
4. **Equipment `exclusive` vs `shareable`.**
5. **Rep-compensation validity floor.**
6. **`enabled_modules` needs a consumer.**
7. **"Adults only" is a proxy for "trains" and will not hold.** Per-member flag when it breaks. **Do not build now.**

### Option B ideas — parked until A has been used for a month

Typed materials · artifact combination · legible accumulation. **No fungible currency, no conversion path.**

### Endure — the separate product

Parked whole. **Not a module, not a register, not a surface.**

### Everything else

- **Vault favorites → real per-profile persistence** (currently `localStorage`).
- **Quality — a rating with no consumer.**
- **Re-forge reach across the 13.**
- **Service worker + app-shell cache.** Gate B, **deserves its own careful pass.**
- **Backup posture.** A distribution blocker.
- **`sandbox_exec`** — one question to Lovable.
- **`quests.approved_by` sweep toward the `approve_redemption` pattern.**
- **Ghost successor cleanup.**
- Quick Add default EXPANDED on empty board · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar event-pill member color · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · flat/peer holds · photo avatars · kid-vs-kid impersonation · role-label retirement ("Parent/Kid") · favorites on the wall · the timezone nudge · the "how Scott & jAIne work" collaboration profile.

### Gate C removals — the pre-production sweep

**`/quest-log` and `/hearth-log` route deletion** joins **prod test-object cleanup** on this shelf. Both are things that exist because we are still building, and both come out immediately before a stranger can reach the product. **Not deletion-pending. Scheduled.**

### Forge at stranger scale — what does NOT come along

Injury-prescription liability posture · commercial-gym equipment model. **Both are stranger-scale problems and Forge is household-scoped.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.** A retained household becomes worth $25 + $10 + $5 instead of $25 flat.

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **DELETING `/quest-log` AND `/hearth-log` "ONCE THE SLATE IS TRUSTED" — SUPERSEDED 2026-08-01.** The Slate IS trusted and the routes stay anyway. **The gate was wrong because it assumed the only reason to keep them was doubt.** The Hearth Log is a working debug surface with a live consumer — Scott — and it earned its keep twice in two days as the readable record of what the feed actually wrote. **Rescheduled to Gate C, alongside prod test-object cleanup.**
- **FIXING THE `verbLabel` ENUM LEAK — DECLINED 2026-08-01.** Recon proved the leak reaches exactly one unlinked page that we are keeping deliberately. **A fix would cost a job to change a string nobody encounters by accident.** ⚠️ **The accepted cost: an unswept vocabulary surface will eventually say "quest" while every other screen says bounty, and someone will read it at 11pm and believe it.**
- **"FOUR INDEPENDENT VERB→DISPLAY IMPLEMENTATIONS" AS A DIVERGENCE PROBLEM — DOWNGRADED 2026-08-01.** Source read proved three are word-for-word identical and the fourth is a legacy variant on the page being kept. **That is duplication, not divergence. It is not the `isActiveQuest` failure mode and should not carry that severity.** Still worth collapsing eventually; no longer evidence of a systemic split.
- **A BRIGHT-HEAVY SLATE AS A PROBLEM — RESOLVED 2026-07-31, NOT PARKED.** Scott lived with `13 need doing` for a day and called it: it reads as honest. **The premise that a dim Slate is the healthy state was jAIne's, and it was wrong — the Slate's job is an accurate count, not a comfortable one.** If a Monday ever spikes it past comfortable, the lever is grouping, never re-hiding work.
- **TIGHTENING `wall_request_redemption` TO MATCH THE RLS POLICY — DECLINED 2026-07-31.** It would have forced `_profile_id = auth.uid()`, meaning **only the household owner could ever redeem from the wall.** The recon treated the older RLS policy as correct and the newer RPC as deviant; it is the other way round.
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.** north-star asserted it; the wall has done all four by design for two weeks. **The doc was wrong, not the build.**
- **Marquee/scrolling titles — DECLINED.** Six simultaneously scrolling titles on a wall is a slot machine.
- **Title `maxLength` — DECLINED.** Killed by the photos: the truncating titles were 14 and 20 characters. **The container was the defect.**
- **The ADULTS ONLY badge as the truncation culprit — DISPROVEN.**
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED.** Two independently-authored surfaces wrap correctly with no primitive. **The job is deleting a class at three sites.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.** ✅ **And disproven on a real month boundary 2026-08-01: five monthly rows, zero duplicates.**
- **Ember tier heat on the Slate — SUPERSEDED.** Brightness on the Slate means state, full stop.
- **A segment control for Slate/Ledger — SUPERSEDED.** Symmetric tabs imply equal weight and would have lied.
- **The Ledger's record-vs-scrapbook fork — DISSOLVED, not decided.**
- **A numeric collapse threshold on the Slate — DECLINED.** Collapse by state; it self-sizes.
- **Capacitor — DECLINED**, with two named reopen triggers.
- **Kid-auth — DECLINED.**
- **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** Embers are the only currency of the platform economy.
- **"Layer" — retired.**
- **Cinder and Holt — DECLINED.** **The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30; the rename is LANDED as of 2026-07-31.**

---

## 🟠 THE ROW PRIMITIVE — STILL OPEN, STILL SMALL

The Slate wraps titles correctly with no primitive at all, and the board always did. **The job shrank to deleting a declared `truncate` at three sites.** ⚠️ **The wall is the open half:** it is a fixed-height ambient rail, and wrapping there may push rows out of view where wrapping on a scrollable phone surface costs nothing. **Cap-at-two-lines may earn its keep on the wall and only on the wall. Visual success criterion — Scott's eyeball, not a brief.**

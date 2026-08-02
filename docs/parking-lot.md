# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

**THE SCREEN COPY PASS** is its own running section below. It is a review inventory, not a backlog item.

⚠️ **PHASE 2 IS OUT OF SCOPE FOR THIS DOC'S NOW AND NEXT.** Stripe, the Founding Guildhall, refunds, tax, COPPA and the privacy policy are real and next, but they sit after a complete and ready Emberhold. Ideas belonging to them live in LATER until Phase 1 closes.

---

## Inbox (untriaged)

- **`ledger.tsx` and `slate.tsx` each carry their own `og:title`; the other 31 routes do not.** Surfaced during the 08-01 page-title sweep. Not a defect. **Either 31 are missing it or 2 have extra.** Cheap either way; needs a call on which is right.
- **An application-level export routine.** Offered by Lovable during the 08-02 backup conversation: a server function dumping tables to a safe format, as a secondary backup Scott controls. **Real idea, wrong time.** It is a build to solve a problem a support answer might solve for free. **Revisit only if Lovable comes back without a confirmed retention window.**

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**The rule, established 2026-07-31: DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**The second rule, established 2026-08-01: NO EM DASHES IN USER-FACING COPY — and none in the briefs handed downstream, because the second is how the first happens.** 125 removed on 08-01 across 43 files. ⚠️ **En dashes in time and date ranges are typographic convention, not the tell. A bare glyph used as a "nothing set" placeholder is a display value, not prose. Both stay.**

**The disease it treats:** design-rationale phrasing from jAIne's build briefs gets rendered as UI copy. The app ends up explaining its own architecture to a nine-year-old. *"Everything with a future," "recurring duties, one line, forever," "here until they're approved"* were all brief text, verbatim, on the glass. **Punctuation escapes the same way the reasoning does.**

**The comparison point for what GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to an adult"* and the wall's *"AN ADULT APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the approval gate, which is invisible and is the thing people get wrong.

**And what good VOICE looks like:** *"Clear skies ahead."* beat the flat *"Nothing scheduled."* because the eyebrow above it already reads ON THE HORIZON. *"Nothing on the fire for this day"* was the failure mode of the same instinct: reaching for voice on a line that only needed to state a fact.

**Batch before firing.** Four strings is not worth a Code job on its own; accumulate a few screens.

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31, revisited 08-01, closed 08-02** | Eyebrow → EVERYTHING THAT'S LIVE. Standing + One-offs subtitles deleted. Collapsed-group label → `{n} done`. ✅ **`SlateEmpty()` rewritten and LIVE: "Repeating bounties live here. The trash, the dishes, Monday laundry." The `laundry,it` defect died with the string.** |
| **The Ledger** | ✅ **Reviewed 07-31** | Own copy already clean; inherits the Slate's link. |
| **Auth / sign-in** | ✅ **Reviewed 08-01** | Subhead deleted. WHAT IS EMBERHOLD block kept. ⚠️ **Lines 22 to 24 carry a design rule as a source comment. Now captured in `decisions.md`.** |
| **Campaigns** | ✅ **Reviewed 08-01** | Description deleted entirely. |
| **Calendar** | ✅ **Reviewed 08-01** | Day empty state → **"The day is clear."** |
| **Briefing / Hub** | ✅ **Reviewed 08-01** | Horizon empty state → **"Clear skies ahead."** ⚠️ *The FAB overlapping the Campaigns progress bar is layout, not copy.* |
| Board | ⬜ unreviewed | |
| Vault | ⬜ unreviewed | ⚠️ *"Each request goes to an adult"* is a KEEP. |
| Lists | ⬜ unreviewed | ⚠️ The "5 OPEN · 348 DONE" fossil counter is a separate defect, not copy. |
| Ranks / leaderboard | ⬜ unreviewed | |
| You / profile | ⬜ unreviewed | |
| Create / edit | ⬜ unreviewed | |
| Bounty detail (`quest.$id`) | ⬜ unreviewed | |
| The wall | ⬜ unreviewed | ⚠️ The only semi-public surface. Ranks above the private screens. ⚠️ **It holds independent copies of strings that also live in `Briefing.tsx`; read them as a pair.** |
| First run — adult (6 screens) | ⬜ unreviewed | ⚠️ **Heavily rewritten by the em-dash sweep; worth a voice read.** |
| First run — kid / joiner (3 screens) | ⬜ unreviewed | |
| Onboarding — first bounty | ⬜ unreviewed | ⚠️ High-stakes screen; read it whole. |
| Notifications | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | **Deliberate debug surface, kept until Phase 2 removal.** Unswept on purpose so it stays obvious which surface is which. |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 OWN-SESSION VS PER-MEMBER-AUTH. THIS IS NOW CRITICAL PATH #1.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. ⚠️ **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they are conditional.** ⚠️ **`useActiveMember().role` reads the switched-to profile client-side, while every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is.** **Until this is decided, four items can be listed but not scoped, which is the single largest reason the board looks thinner than the work is.** **It is a decision session. Do not brief it as a build.**
- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller.** The column does two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels. **(b) is jAIne's lean.** Design call, not a bug fix.
- **🟠 SHOULD `logActivity` MOVE SERVER-SIDE?** It is a client-side call bolted onto each site by hand, so every new site can forget, and one did, silently, for the entire life of the wall's approve path. **The mechanism fix is the definer RPC writing its own log row.** ⚠️ **THE BLOCKING QUESTION: does the display label belong in the DB at all?** 08-01 weakened the case that it does, because the three renderers are **not** byte-identical. **Answer this before any migration is written, or the schema decision gets made by accident.**
- **🟠 IS `parent_self_redeem` SKIPPING THE APPROVAL GATE CORRECT?** It inserts `status='approved'` with `decided_by = auth.uid()` in one call. An adult redeeming their own embers is requester and approver simultaneously. **Reads as deliberate in the code and has never been written down.** Ratify it or change it, but stop having it be an undocumented behavior of the economy.
- **⚠️ THE ONBOARDING ADD-MEMBER AVATAR PICKER.** Scott's report: a member added during onboarding could not choose an avatar. **If true, every non-creator in every hold starts null by construction, and the wall was simply the first surface where a null face was big enough to notice.** ⚠️ **This supersedes the old "avatar render fallback, check the wall" item, which was unfalsifiable from Scott's hold because everyone in it picked a face during first run.** **Needs one free recon before it becomes a decision: does the flow offer a picker, and what renders when `avatar` is null.**
- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** **jAIne's lean: do NOT auto-default and do NOT guess.** The roster is the one place a hearthmate says "this is me." What is missing is a **nudge**: make the empty seat tappable, routing to the picker. **The one exception is the wall**, where a silhouette is a dead spot on an ambient display. **Raised 2026-07-29, never ratified. Related to the item above but not the same question.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** Creation stays ungated. **What is unsettled is which surface is wrong.**
- **WHERE DOES THE REST-TIMER ALERT FIRE — the garage screen, or the pocket?** **jAIne's lean: the screen owns the SESSION; phones own the TIMER.** Taste call in a room jAIne cannot see. **Scott's.**
- **In the agenda lane, does the rest timer SHARE the cell with load/weight/reps or REPLACE it?** Scott's sketch has it replace. **jAIne flagged the objection, never ratified.**
- **Does the LLM plan BOTH sessions in one call, or one call per person?** **The contention feature effectively requires one call; confirm that is intended before it becomes an implicit constraint.**
- **THE FORGE'S SHAPE — Option A vs Option B, inside a household-scoped frame.** **A ships first; this decision is only about whether B ever happens.**
  - **(A) TOOL ONLY.** Prescription-first, pre-session gate, catalog-backed substitution, contention resolution, reason string, log, rest timer, display mode. **The bar is a USER TEST: Scott and May stop opening Fitbod.**
  - **(B) TOOL PLUS ONE SEALED GAME.** Effort produces typed materials; materials combine into artifacts; artifacts accumulate as legible evidence. No fungible currency.
  - **What B owes:** (1) do materials pool, or are they claimed at commitment? (2) what are they typed **on**? (3) does a collection survive the calibration check?
  - **The standing risk:** the game is the most interesting part to build and the tool is the part that pays. **B does not get designed until A has been used for a month.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. **A one-time SKU funds a one-time year.** **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.** ⚠️ **Phase 2, but the clock does not care which phase it is in.**
- **Module navigation.** Seven tabs is one past mobile comfort. ✅ **The Slate/Ledger half is ANSWERED and shipped.** **Forge's contained-route posture still defers the module half.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.**
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott has not called it.
- **Unify `quest.audience` and `reward.audience`?** Two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 THE OWN-SESSION FORK.** See OPEN DECISIONS. **It is a session, and it is the one that unlocks estimates.**
- **🔴 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper, never Fully Kiosk.** If Chrome withholds it, remote-debug over USB: **Chrome's Manifest panel names the failing criterion in plain text**, which beats another round of theories.
- **⬜ BACKUP: TRIGGER THE EXPORT.** **Cloud → Advanced settings → Export data.** Free, self-service, no credits. ⚠️ **Lovable could not confirm PITR, schedule or retention from the project side. Zero backups to one backup is the largest single risk reduction available on this board.** Then ask a human at Lovable for the retention window.
- **⬜ THE ONBOARDING ADD-MEMBER AVATAR RECON.** Free, read-only, and it may reclassify a cosmetic item as a default-path defect.
- **🖊️ THE SCREEN COPY PASS.** Slate ✅ Ledger ✅ Auth ✅ Campaigns ✅ Calendar ✅ Briefing ✅.
- **⬜ AVATAR ROSTER TRANSPORT.** Storage upload → picker slotting → free/paid split. **The last piece of Gate A's tail.**
- **`Testing retired` stays retired** once its successor's date arrives. One look.
- **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`** — a failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- **Two derivations of role** — `profiles.role` vs `user_roles` in `useMyProfile()`. ⚠️ **And a third split: `useActiveMember().role` vs `has_role(auth.uid())`.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward; verify before building.**
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- **The Briefing's FAB overlaps the Campaigns progress bar.** **Layout, Scott's eye.**
- **Prod test-object cleanup, now three objects.** `Testing redemption tracking` in the Ledger; `Testing retired` in the Slate's Retired section, **and canon records no un-retire affordance**; `testing approve` from the 08-02 verification walk. ⚠️ **`testing approve` minted 10 real embers to Mia and deleting the bounty will not unwind them.** **The open question underneath: every glass walk that touches the economy puts real currency into a real kid's balance. Do test mints get cleaned up, or accepted as the cost of verification?**
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Grant-revoke verification probe job.** Drafted, deferred nine times.
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15 to 20 min), screenshot from the phone.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

---

## NEXT (soon — off the critical path)

### Toolchain

- **⚠️ BUN IS THE PACKAGE MANAGER. NEVER npm OR yarn.** ✅ **bun 1.3.14 installed 2026-08-02; it had never been installed at all.** **Name this line in every Code brief, exactly like `routeTree.gen.ts`.**
- **🔵 `bun audit` BASELINE BANKED 2026-08-02: ten findings, all dev-tree, none shipping.** Re-run after any dependency change; ignore otherwise. ⚠️ **Lovable's panel scans the 55-package runtime tree and reports two. Different sets, not a contradiction.**
- **🔵 THE 47 TANSTACK TYPECHECK ERRORS — REAL, AND DELIBERATELY NOT FIXED.** One class: a missing `search` property on navigate, redirect and Link calls. **Type layer only, zero runtime impact, every affected path works daily.** **36 call sites across every route for zero user-visible change is not pre-stranger work.** **Revisit only if a router upgrade forces it.**
- **⚠️ `routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** Every build surfaces it; every agent has to know to throw it away. **Name it in every brief.**
- ✅ **`progression.test.ts` vitest types — FIXED 2026-08-02** (`9963e12`). Eleven consecutive polluted job outputs, closed by one line in `tsconfig.json`.
- ✅ **`package-lock.json` DELETED.** ⚠️ **`query_quest.mjs` is still untracked in the working tree.**
- **Claude Code's "sync before reading" rule needs a forcing function.** ✅ **Worked nine times. Keep the line in every Code brief.**
- **`wall_request_redemption` IS CALLED FROM THE VAULT AND ITS NAME LIES.** Deliberate debt; renaming an RPC is a migration. **Rename when something else takes that function to Lovable.**
- **⚠️ PLAN-MODE ITERATIONS ARE BILLED.** **Plan mode stays the right call for migrations, because a code revert is not a database revert, but the review is ONE pass.**

### Onboarding, phase three

- **The kid joiner flow.** 🔵 **DOWNGRADED 2026-08-02, Scott's call.** Adult-supervised at the exact moment it runs. **Will surface itself. Not a blocker.**
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read** now that it is live, renamed and heavily repunctuated. ⚠️ **Part of the screen copy pass.**
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for Phase 1.** Option A's shape lives in `master-spec.md` Part II; this section holds build order only.*

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

- **Vault favorites → real per-profile persistence** (currently `localStorage`, so they do not follow a kid to another device).
- **Quality — a rating with no consumer.**
- **Re-forge reach across the 13.**
- **The offline shell / app-shell cache — DEPRIORITIZED, see LATER.**
- **Ghost successor cleanup.**
- Quick Add default EXPANDED on empty board · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar event-pill member color · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

**The offline shell / app-shell precache** (deliberately deferred 08-01; reopens with PWA push, same mechanism) · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · flat/peer holds · photo avatars · kid-vs-kid impersonation · role-label retirement ("Parent/Kid") · favorites on the wall · the timezone nudge · the "how Scott & jAIne work" collaboration profile · an application-level export routine.

### PHASE 2 — money and paperwork

**Stripe · the Founding Guildhall build (checkout, webhook, entitlement write) · refund posture · tax posture · COPPA · the privacy policy (which must name `flock.js`) · the landing page · the Pip-guided install tutorial · PostHog.**

⚠️ **THIS WAS ONE ROW ON THE CRITICAL PATH AND IT IS AT LEAST FIFTEEN.** It was never decomposed, which is why the board looked thin. **The decomposition session is itself a task, costs zero credits, and would tell us within an hour whether Phase 2 is three sessions or ten.**

⚠️ **`system_flags` PRECONDITION.** The table's read policy is `TO authenticated USING (true)` and nothing constrains what goes into a bare key/value store. **Before the first non-public or non-boolean flag lands there — a Stripe mode switch, a rollout percentage, a kill switch — the read policy must be narrowed.** Harmless today because the one row is a global `false`.

### Pre-production sweep

**`/quest-log` and `/hearth-log` route deletion** joins **prod test-object cleanup** on this shelf. Both exist because we are still building, and both come out immediately before a stranger can reach the product. **Not deletion-pending. Scheduled.**

### Forge at stranger scale — what does NOT come along

Injury-prescription liability posture · commercial-gym equipment model. **Both are stranger-scale problems and Forge is household-scoped.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.** A retained household becomes worth $25 + $10 + $5 instead of $25 flat.

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **THE 47 TYPECHECK ERRORS AS AN npm ARTIFACT — DISPROVED 2026-08-02.** The hypothesis was that a stray `npm install` against a stale `package-lock.json` had resolved a router version whose types were stricter. **Identical error count under npm's 1.170.17 and bun's 1.170.16.** The errors are real and pre-existing. ⚠️ **Eleven consecutive Code jobs reported the single vitest error and none of them mentioned the other 47. That is a fact about how agents report typecheck output, not about the codebase.**
- **"ADULT PIN STORED IN PLAINTEXT IN localStorage DURING SIGNUP" — DISPROVED 2026-08-02.** The scanner is holding a memory of the pre-07-26 signup path. **Recon found only two localStorage keys in the entire application and neither is PIN-related.** ⚠️ **A stale scanner entry is worse than a wrong one: it dilutes the panel and trains the reader to skim.**
- **REVOKING `sandbox_exec`'s EXECUTE GRANTS — DECLINED 2026-08-02.** Platform-managed Lovable Cloud role for the agent sandbox, migration runner and schema introspection. **`postgres` is a member with ADMIN OPTION and inherits from it.** Revoking would break migrations and agent introspection. **Narrowing is a Lovable support conversation, never a manual revoke.**
- **`approve_quest()` AS A NEW RPC WITH FIVE REWRITTEN CALL SITES — DECLINED 2026-08-02.** It was the faithful match to `approve_redemption`'s literal shape and the wrong trade. **A trigger validates the actor at all five sites at once with zero client changes**, and the alternative put the Briefing's credit-reassignment and the wall's PIN flow inside the blast radius of a security fix. **Reopens only if there is an independent reason to centralize approval, which there currently is not.**
- **"AVATAR RENDER FALLBACK, CHECK THE WALL" — SUPERSEDED 2026-08-02.** The item was unfalsifiable from Scott's hold: every member of it picked a face during first run, so the wall always looks correct. **The real question is whether onboarding's add-member flow offers a picker at all.** Renamed and moved to OPEN DECISIONS.
- **DELETING A BOUNTY ORPHANING ITS CALENDAR EVENT — KILLED 2026-08-01, NEVER REAL.** Bounties do not create calendar events. **A tracking item with no named evidence and no date is a rumor.**
- **THE SLATE PANEL/HEADER MISMATCH — KILLED 2026-08-01, NEVER REAL.** There is no "Done today" header on the Slate. jAIne invented the label from a 07-31 screenshot. **A label in a screenshot is not necessarily on the surface at all.**
- **DELETING THE STANDING-DUTIES BLURB — REVERSED BEFORE IT SHIPPED, 2026-08-01.** It is the section's EMPTY STATE, so the screen shows nothing and the copy is all there is. **The delete-don't-reword default has an exception and this is it.** ✅ **The rewrite shipped 08-02.**
- **THE OFFLINE SHELL — DEPRIORITIZED 2026-08-01, NOT KILLED.** Household wifi is pervasive and a chore board is not what anyone needs in a dead zone. **The cache strategy carries all of the risk for almost none of the benefit**, and a caching bug is the one defect on this board that cannot be fixed by pushing a fix. **Reopens if PWA push is ever built, because they are the same mechanism.**
- **DELETING `/quest-log` AND `/hearth-log` "ONCE THE SLATE IS TRUSTED" — SUPERSEDED 2026-08-01.** The Slate IS trusted and the routes stay anyway. **The gate assumed the only reason to keep them was doubt.** The Hearth Log is a working debug surface with a live consumer. **Rescheduled to the pre-production sweep.**
- **FIXING THE `verbLabel` ENUM LEAK — DECLINED 2026-08-01.** The leak reaches exactly one unlinked page we are keeping deliberately. ⚠️ **The accepted cost: an unswept vocabulary surface will eventually say "quest" while every other screen says bounty, and someone will read it at 11pm and believe it.**
- **"FOUR INDEPENDENT VERB→DISPLAY IMPLEMENTATIONS" AS A DIVERGENCE PROBLEM — DOWNGRADED 2026-08-01.** ⚠️ **And the three are NOT byte-identical: `wall.tsx`'s `event_created` omits "to the calendar."** Duplication that has already drifted once, still not divergence.
- **A BRIGHT-HEAVY SLATE AS A PROBLEM — RESOLVED 2026-07-31.** Scott lived with `13 need doing` and called it honest. **The premise that a dim Slate is the healthy state was jAIne's, and it was wrong.**
- **TIGHTENING `wall_request_redemption` TO MATCH THE RLS POLICY — DECLINED 2026-07-31.** It would have meant **only the household owner could ever redeem from the wall.**
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.** The doc was wrong, not the build.
- **Marquee/scrolling titles — DECLINED.** Six simultaneously scrolling titles on a wall is a slot machine.
- **Title `maxLength` — DECLINED.** The truncating titles were 14 and 20 characters. **The container was the defect.**
- **The ADULTS ONLY badge as the truncation culprit — DISPROVEN.**
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.** ✅ **Disproven on a real month boundary 2026-08-01: five monthly rows, zero duplicates.**
- **Ember tier heat on the Slate — SUPERSEDED.** Brightness on the Slate means state, full stop.
- **A segment control for Slate/Ledger — SUPERSEDED.** Symmetric tabs imply equal weight and would have lied.
- **The Ledger's record-vs-scrapbook fork — DISSOLVED, not decided.**
- **A numeric collapse threshold on the Slate — DECLINED.** Collapse by state; it self-sizes.
- **Capacitor — DECLINED**, with two named reopen triggers.
- **Kid-auth — DECLINED.** · **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** Embers are the only currency of the platform economy.
- **"Layer" — retired.** · **Cinder and Holt — DECLINED. The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30; the rename is LANDED as of 2026-07-31.**

---

## 🟠 THE ROW PRIMITIVE — STILL OPEN, STILL SMALL

The Slate wraps titles correctly with no primitive at all, and the board always did. **The job shrank to deleting a declared `truncate` at three sites.** ⚠️ **The wall is the open half:** it is a fixed-height ambient rail, and wrapping there may push rows out of view where wrapping on a scrollable phone surface costs nothing. **Cap-at-two-lines may earn its keep on the wall and only on the wall. Visual success criterion — Scott's eyeball, not a brief.**

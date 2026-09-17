# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

**THE SCREEN COPY PASS** is its own running section below. It is a review inventory, not a backlog item.

⚠️ **THE PHASE BOUNDARY MOVED 2026-09-11.** Gate E (closed beta) now runs ahead of Gate C (money and paperwork). **Beta-grade work — the landing page, a short true privacy policy, PostHog, auth email deliverability — is NOW and NEXT.** Stripe, the Founding Guildhall build, refunds, tax and full COPPA posture stay in LATER until the beta has reported.

---

## Inbox (untriaged)

- **`ledger.tsx` and `slate.tsx` each carry their own `og:title`; the other 31 routes do not.** Not a defect. Either 31 are missing it or 2 have extra. Cheap either way; needs a call on which is right.
- **An application-level export routine.** Offered by Lovable during the 08-02 backup conversation. **Real idea, wrong time.** Revisit only if Lovable comes back without a confirmed retention window.
- **Should the reward audience enum get its own label map?** The Vault's `household` / `adults_only` is a separate enum from the quest audience and now has two hardcoded "Keepers only" strings. **Two sites is not yet drift.**
- **Streaks on standing duties.** `series_id` made them possible without a second migration. **Not scoped, not designed, and it must pass the daily-habit test before it earns a line of vertical height.** ⚠️ **A streak is also a guilt mechanic wearing a reward costume — the exact thing same-row roll-forward was built to kill. Design it carefully or not at all.**
- **Does the wall want last-done?** It landed on the Slate, bounty detail and Board cards. **The wall is a different density problem and nobody has looked.**

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**The rule, established 2026-07-31: DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**The second rule, established 2026-08-01: NO EM DASHES IN USER-FACING COPY — and none in the briefs handed downstream, because the second is how the first happens.** *En dashes in time and date ranges are typographic convention, not the tell.*

**The third rule, established 2026-08-03: VERTICAL HEIGHT ON A SCROLLING PHONE BOARD IS ITS OWN ARGUMENT.** ⚠️ **Amended 2026-09-11: it is an argument, not a trump card.** jAIne used it to keep last-done off Board cards; the line cost about twelve pixels and the rows had room. **Measure the height before spending the argument.**

**The fourth rule, established 2026-09-11: A VOCABULARY SWEEP MUST SEARCH SYNONYMS, NOT JUST THE RETIRED WORD.** The 07-31 coverage grep searched "Parent" and "Kid" and passed clean. **Four "adult" strings survived it**, one of which then shipped past a fix that edited the same component.

**The disease it treats:** design-rationale phrasing from jAIne's build briefs gets rendered as UI copy. **Punctuation escapes the same way the reasoning does.**

**The comparison point for what GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to a Keeper"* and the wall's *"A KEEPER APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the approval gate, which is invisible and is the thing people get wrong.

⚠️ **TOUCHED IS NOT REVIEWED.** The Keeper and Kin rename swapped a word on eleven screens. **None of them got a voice read.**

**Batch before firing.**

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31, closed 08-02** | *EditPanel picker relabeled 08-03; last-done line added 09-11. Copy still current.* |
| **The Ledger** | ✅ **Reviewed 07-31** | |
| **Auth** | ✅ **Reviewed** | |
| **Campaigns** | ✅ **Reviewed** | |
| **Calendar** | ✅ **Reviewed** | |
| **Briefing** | ✅ **Reviewed** | *FAB overlap fixed 09-11.* |
| **The wall** | ⬜ unreviewed | ⚠️ **Ranks first. Nine independent strings touched by the rename, never read. Campaign rotation rebuilt 09-11.** |
| **The Vault** | ⬜ unreviewed | ⚠️ **Seven strings touched by the rename. Keeper empty state added 09-11; the Kin empty state still says "adult." See NOW.** |
| **The Board** | ⬜ unreviewed | *Last-done line added 09-11.* |
| **Quest detail** | ⬜ unreviewed | |
| **Lists** | ⬜ unreviewed | ⚠️ **Rows truncate mid-line; the add bar's section chip truncates to "NO SEC…". See NOW.** |
| **Onboarding — add family** | ⬜ unreviewed | ⚠️ **PipSpark still says "this adult" one line below a Keeper PIN label. See NOW.** |
| **Onboarding — recap** | ⬜ unreviewed | ⚠️ **"Adults turn dishes..." and "Adults approve." See NOW.** |
| **Onboarding — first bounty** | ⬜ unreviewed | ⚠️ **High-stakes screen; read it whole.** |
| **PipHelp** | ⬜ unreviewed | ⚠️ **Kin-facing topic says "rewards the adults set up." See NOW.** |
| **Notifications** | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | **Deliberate debug surface, kept until the pre-production sweep.** |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🟠 DOES THE ACTIVITY FEED'S RESOLVED NAME STAY FROZEN?** Settled in shape: the feed stores facts, splitting into `actor_id` (who clicked) and a validated `subject_profile_id` (who gets credit), names resolving at render. **Unsettled: frozen at write time or looked up live.** If Mia becomes Amelia, does history say Mia? **Blocks the backfill design, not the schema shape.**
- **🟠 IS `kids-only` A DEAD AUDIENCE VALUE?** Scott is ~92% confident no bounty has ever used it. **"Kids only" read as a rule a Keeper sets; "Kin only" reads as a description of who is around — the tag was borrowing authority from the word "kid."** One grouped read of `quests.audience` across all thirteen holds settles it. **Do not kill it on 92%.**
- **🟠 DOES THE WALL WANT AN AUDIENCE BADGE?** The phone's `QuestCard` shows one; `wall.tsx`'s `BountiesList` shows none. **Cross-surface asymmetry on the only semi-public surface.** Not obviously a defect — the wall is an ambient rail with different density constraints. **Scott's eye.**
- **🟠 SHOULD LISTS GET AN AUDIENCE FLAG?** ⚠️ **Surfaced 09-11: Lists are hold-wide with no audience pattern at all, which is a real exception to a load-bearing platform pattern.** Live consequence: a trip list containing gift items is readable by every Kin in the hold. **The audience pattern already exists on quests and rewards and would be reused, not invented. Not scheduled. jAIne's lean: leave it — a list nobody can see is a list nobody maintains, and the membrane says Lists stay clean utilities.**
- **🟠 DOES A NULL `series_id` DESERVE ITS OWN EMPTY STATE?** ⚠️ **Today a null lineage and a genuinely-never-done duty both read "Not done yet."** That is the only untruth the last-done feature can tell. **Depends on the backfill's null count, which was requested and never reported.**
- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** The picker IS offered during add-member and CAN be skipped, leaving a themed empty circle. **So this is a nudge, not a fix.** **jAIne's lean: do not auto-default. Make the empty seat tappable, routing to the picker.** The one exception is the wall, where a silhouette is a dead spot on an ambient display. **Raised 07-29, informed 08-03, still unratified.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?** Creation stays ungated. **What is unsettled is which surface is wrong.**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED as the v1 SKU at $25. ⚠️ **The clock got louder 09-11: Forge is declined, so there is no longer any non-stranger path to break-even. Every dollar of the $636 now rides on stranger households.** **Hard deadline: if Emberhold is still running in July 2027, this has to be decided by then.**
- **Module navigation.** Seven tabs is one past mobile comfort. ✅ **The Slate/Ledger half is ANSWERED and shipped.** ⚠️ **The module half is now moot — Forge was the only module and it is declined.** **What remains is whether seven tabs is one too many on its own merits.**
- **⚠️ Staging / dev database — do we need one before the beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.** ⚠️ **The Gate E reorder makes this sooner, not later — real beta households on the same database as a dev pointer is a different risk than thirteen friends.**
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** Waiting on concrete examples from Scott.
- **The founder paywall flip — timing only, mechanism is built.** **The grandfather write must run as `service_role`.** Travels with the avatar free/paid split.
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: visible to the Kin or Keeper-only, and what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott has not called it.
- **Unify `quest.audience` and `reward.audience`?** Two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.
- **What replaces `/setup/intent`'s parking trigger?** ⚠️ **It was parked "until Forge is built." Forge is declined, so the trigger can never fire.** **Needs a new disposition: delete, repurpose, or re-park against something real.**

---

## NOW (this is the next work)

- **🔴 THE LANDING PAGE.** Critical path. **Doubles as beta recruitment: founding households wanted.** Pure visual, Scott's lane, Lovable's strength. **Write the pitch first — what it is, who it's for, what you are asking of them.**
- **🔴 BACKUP: TRIGGER THE EXPORT.** **Cloud → Advanced settings → Export data.** Free, self-service, no credits. Raised at 09-11 open and still not confirmed. **Thirteen live accounts, no confirmed recoverable backup.**
- **🔴 AUTH EMAIL DELIVERABILITY.** ⚠️ **Promoted 09-11.** It was deferred pending DNS spend; the Gate E reorder makes it load-bearing. **Every beta household's first impression runs through it.** *Inspect any NS-record request before pasting.*
- **🔴 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper, never Fully Kiosk.** If Chrome withholds it, remote-debug over USB: the Manifest panel names the failing criterion in plain text.
- **A SHORT PRIVACY POLICY THAT IS TRUE.** Must name `flock.js`. **Beta-grade, not Gate C grade. This is a writing job, not a legal build.**
- **POSTHOG.** **Day 8 is Gate E's entire exit criterion and it is unmeasurable without instrumentation.**
- **LIST ROW TRUNCATION.** Rows truncate mid-line inside a container with room for three, and the add bar's section chip truncates to "NO SEC…". **Wrap to two lines, truncate past that, tap-to-expand for the rare leftover.** **Tap-and-hold was declined — see KILLED.**
- **THE FOUR "adult" STRINGS.** `onboarding.add-family.tsx` PipSpark · Onboarding Recap ×2 · PipHelp's Kin-facing topic · the Vault's Kin empty state. ⚠️ **Plus the whole-tree grep report, which was requested in the rolodex bundle and never came back.**
- **VERIFY THE PHONE BOARD'S CAMPAIGNS.** The original complaint was the Board; the fix landed on the wall. **The Briefing lists all campaigns, so the Board may already be fine.** One look, no credits.
- **VERIFY THE `series_id` BACKFILL COUNT.** Linked groups versus null. **If null is more than a couple, spot-check by hand.**
- **🔴 GLASS-VERIFY THE THREE RENAME COMMITS (08-03).** `9c83fc4`, `e8223f4`, `f9f731e`. **The Vault as a Keeper and as a Kin, the Board, quest detail, and the wall.** Free.
- **🖊️ THE SCREEN COPY PASS.** Six screens reviewed, eleven touched by the rename without being reviewed. **The wall ranks first.**
- **`board.tsx:149`'s kicker may stutter** against the `<h1>` below it. Delete it if it reads badly. Scott's eye.
- **`Testing retired` stays retired** once its successor's date arrives. One look.
- **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`** — a failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- **Prod test-object cleanup, three objects.** ⚠️ **`testing approve` minted 10 real embers to Mia and deleting the bounty will not unwind them.** **The question underneath: do test mints get cleaned up, or accepted as the cost of verifying the economy on live data?**
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Grant-revoke verification probe job.** Drafted, deferred eleven times. **`SQLSTATE 42501` = FAIL, any application-level error = PASS.**
- **The `quests.audience` grouped read.** Settles whether `kids-only` is dead. Rides along on the next Code job.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15 to 20 min), screenshot from the phone.
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- **Two derivations of role** — `useActiveMember().role` vs `has_role(auth.uid())`. **The fork decision makes this permanent by design rather than pending a fix.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward; verify before building.**

---

## NEXT (soon — off the critical path)

### Beta readiness

- **RECRUIT THE FOUNDING HOUSEHOLDS.** Target 5 to 10. **In order: second-degree strangers (ask each of the thirteen to forward to one family they do not share with Scott), gamer-parent communities where quest/loot/XP is native vocabulary, then big parenting groups posted as a dad rather than a launch.** ⚠️ **Nothing through WCSD. No district parent networks, no school-adjacent local groups. Recruit outside Reno entirely.** See `decisions.md`.
- **Skip beta-tester communities.** They poke an app for ten minutes and leave, which says nothing about day 8.
- **The Founding Guildhall comp list.** Beta households are comped for life. **Record their hold IDs as they join; the entitlement backfills at Gate C.** They do not count toward the 27.
- **Keep the one-shot venues sealed.** Product Hunt, Show HN, a big-subreddit launch, a newsletter feature. **One shot each, and spending it on a beta whose auth email lands in spam is the waste.** After Gate D.

### Toolchain

- **⚠️ BUN IS THE PACKAGE MANAGER. NEVER npm OR yarn.** Name it in every Code brief.
- **🔵 `bun audit` baseline banked 2026-08-02:** ten findings, all dev-tree, none shipping.
- **🔵 THE 47 TANSTACK TYPECHECK ERRORS — real, one class, deliberately not fixed.** **Naming the number in the brief turns a polluted output into a control.**
- **⚠️ `routeTree.gen.ts` drift is confirmed live.** Name it in every brief.
- **⚠️ `query_quest.mjs` is still untracked in the working tree.**
- **Claude Code's "sync before reading" rule needs a forcing function.** Keep the line in every brief.
- **`wall_request_redemption` is called from the Vault and its name lies.** Deliberate debt. **Rename when something else takes that function to Lovable.**
- **⚠️ PLAN-MODE ITERATIONS ARE BILLED.** Plan mode stays right for migrations, because a code revert is not a database revert. **The review is ONE pass.**

### The activity log rework

*Design settled 08-03. Blocked on credits and one open question, not on thinking.*

1. **`subject_profile_id` added and validated**, `actor_id` keeps meaning who clicked. **Four of eight write sites currently conflate them.**
2. **`logActivity` moves server-side.** ✅ **Not a proposal: `admit_pending_member` and `deny_pending_member` already INSERT from inside the SQL function.** The pattern is shipped twice and working.
3. **The backfill.** ⚠️ **Blocked on the frozen-name question in OPEN DECISIONS.**
4. ⚠️ **Canon claims three verb switches exist; the 08-03 recon found two.** `hearth-log.tsx` and `NotificationBell.tsx`. One curl to settle.

### Onboarding, phase three

- **The kid joiner flow.** 🔵 **DOWNGRADED 08-02, Scott's call.** Adult-supervised at the moment it runs. **Will surface itself.**
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read.** Part of the screen copy pass.
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Everything else

- **Vault favorites → real per-profile persistence** (currently `localStorage`, so they do not follow a Kin to another device).
- **Quality — a rating with no consumer.**
- **Re-forge reach across the 13.**
- **Ghost successor cleanup.**
- **The offline shell / app-shell cache — DEPRIORITIZED, see LATER.**
- Quick Add default EXPANDED on empty board · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar event-pill member color · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

**The offline shell / app-shell precache** (deferred 08-01; reopens with PWA push, same mechanism) · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · photo avatars · kid-vs-kid impersonation · favorites on the wall · the timezone nudge · the "how Scott & jAIne work" collaboration profile · an application-level export routine.

**⚠️ FLAT / PEER HOLDS IS NO LONGER JUST BACKLOG. It is the single named reopen trigger for the own-session fork.** Roommates, a nanny, a non-custodial parent. **The walk-up boundary is only sound because physical possession of the device equals household authority, which is true of a family and false of roommates.** Building it reopens per-member auth, kid-auth, and a harder COPPA posture, all at once. **It is not a feature request. It is an architecture change wearing one.**

### GATE C — money and paperwork

**Stripe · the Founding Guildhall build (checkout, webhook, entitlement write) · refund posture · tax posture · full COPPA posture · the Pip-guided install tutorial.**

⚠️ **THIS WAS ONE ROW ON THE CRITICAL PATH AND IT IS AT LEAST FIFTEEN.** **The decomposition session is itself a task, costs zero credits, and would tell us within an hour whether Gate C is three sessions or ten.**

⚠️ **IT NOW RUNS AFTER THE BETA, NOT BEFORE IT.** Building the payment rail before knowing whether day-8 retention exists spends fifteen items on a question that has not been asked.

**⭐ THE AVATAR FREE/PAID SPLIT.** All 48 avatars are live, pickable and rendering. **The 16-open / 32-locked split is the mechanism for a Gate C SKU and belongs with the SKU.** ⚠️ **Building it now means shipping locks on 32 faces with nothing to unlock them with, in front of thirteen live households.** The founder tier-tag verification travels with it.

⚠️ **`system_flags` PRECONDITION.** The read policy is `TO authenticated USING (true)`. **Before the first non-public or non-boolean flag lands there — a Stripe mode switch, a rollout percentage, a kill switch — the read policy must be narrowed.** Harmless today because the one row is a global `false`.

### Pre-production sweep

**`/quest-log` and `/hearth-log` route deletion** joins **prod test-object cleanup** on this shelf. **Not deletion-pending. Scheduled.** ⚠️ **Killing the `kids-only` audience value joins them if the grouped read comes back empty.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

⚠️ **THE CATALOG GOT MORE IMPORTANT 2026-09-11.** With Forge declined, **a one-time $25 SKU is the only revenue mechanism that exists**, and a one-time SKU funds a one-time year. **The catalog is leverage on retention succeeding, not insurance against acquisition failing.**

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **THE FORGE, OPTION A — DECLINED 2026-09-11, SCOTT'S CALL.** A full Fitbod replacement is a year of work — exercise catalog, muscle attribution, substitution mapping, a progression engine — to displace $15.99 a month. ⚠️ **jAIne pitched it at session open on the $192/yr cost-avoidance number and never priced the build.** **Scott's reasoning, and it is the right frame: comparative advantage. His edge is the thing Emberhold already is, and it is not exercise databases.** **CONSEQUENCE: Emberhold now has zero modules, `enabled_modules` has no candidate consumer, Forge display mode is dead as a security surface, `/setup/intent`'s parking trigger can never fire, and every dollar of the $636 rides on stranger households.** **Option B dies with it.** **Reopens only if Scott changes his mind about the trade, which is a taste call, not an analysis.**
- **THE FORGE'S OPTION A vs OPTION B DECISION — DISSOLVED 2026-09-11.** It was only ever about whether B happens. **A is declined, so there is nothing for B to follow.**
- **SWIPE CAROUSEL FOR WALL CAMPAIGNS — SUPERSEDED 2026-09-11, SAME SESSION IT SHIPPED.** ⚠️ **jAIne said two turns earlier that auto-rotate belongs on the wall, then wrote a prompt putting swipe there anyway.** **The pattern was right and the surface was wrong.** It clipped a campaign title mid-word to advertise a gesture nobody walking through a kitchen will make, on a screen where canon had already declined marquee text for exactly that reason. **Replaced by the timed rolodex rotation.**
- **CROSSFADE AS THE WALL'S CAMPAIGN TRANSITION — DECLINED 2026-09-11.** **A crossfade is an ad rotator.** A card turning on a hinge reads as a mechanical object, which is the right register for something bolted to a kitchen wall. **It also produces a mid-turn card edge, which is a place for light to catch.**
- **TAP-AND-HOLD TO REVEAL TRUNCATED LIST TEXT — DECLINED 2026-09-11.** **An invisible gesture solving a problem caused by wasted space.** The rows carry enormous vertical padding around one clipped line in a container with room for three. **Wrap to two lines first; tap-to-expand handles the rare leftover.**
- **A REAL LIST IMPORTER — DECLINED 2026-09-11.** CSV parsers, Google Keep and Reminders integrations, field mapping. **Scope creep in a costume.** **Paste-to-split makes the clipboard the API, so any AI, notes app or email is a list source for free, with no schema and no new system.**
- **PASTE-TO-SPLIT IN BOUNTY CREATION — DECLINED 2026-09-11.** Bulk-minting bounties is exactly what cluttered the board. **Lists only.**
- **CAMPAIGNS AND THE BOARD AS A CHECKLIST SURFACE — DECLINED 2026-09-11, BY EXPERIMENT.** Scott built a PAX interest-and-to-do list as bounties and it cluttered the board. **Not a campaigns defect — the membrane working.** Lists already had sections and open/done counts. **The finding was the routing-around, not a missing feature.**
- **LAST-DONE KEPT OFF BOARD CARDS — REVERSED 2026-09-11, SCOTT'S CALL.** jAIne cited vertical height from the spec. **The line costs about twelve pixels and the rows had room.** Same failure shape as avatar transport on 08-03.
- **PER-MEMBER AUTH — DECLINED 2026-08-03.** Rejected on four grounds: kids have no email so it requires a credential path and `kid-auth` was already declined; the wall cannot benefit and it adds a second authority model rather than replacing the first; it creates accounts for under-thirteens, which is the literal COPPA trigger; and switching becomes sign out and sign in on the shared family device. **REOPENS ONLY ON FLAT OR PEER HOLDS.**
- **"KIDS ONLY WAS ALWAYS A LIE" — jAINE'S ARGUMENT, WRONG, KILLED 2026-08-03.** **The audience field has THREE values and `kids-only` is a real restriction that does exclude Keepers.** The stop-clause caught it before it shipped.
- **"AVAILABLE TO ANYONE" — DELETED 2026-08-03.** It never read `quest.audience` at all. **A `parents-only` bounty could show the Keepers only badge and a line claiming it was available to anyone, on the same card.**
- **"THE Parent/Keeper RENAME WOULD SEAM THE FEED HISTORY" — jAINE'S ARGUMENT, WRONG, KILLED 2026-08-03.** `actor_label` stores member NAMES, not role words.
- **AVATAR ROSTER TRANSPORT AS OUTSTANDING WORK — CORRECTED 2026-08-03.** It sat on the critical path for weeks while working.
- **THE 47 TYPECHECK ERRORS AS AN npm ARTIFACT — DISPROVED 2026-08-02.**
- **"ADULT PIN STORED IN PLAINTEXT IN localStorage" — DISPROVED 2026-08-02.** **A stale scanner entry is worse than a wrong one.**
- **REVOKING `sandbox_exec`'s EXECUTE GRANTS — DECLINED 2026-08-02.** Platform-managed.
- **`approve_quest()` AS A NEW RPC WITH FIVE REWRITTEN CALL SITES — DECLINED 2026-08-02.** **A trigger validates the actor at all five sites at once with zero client changes.**
- **DELETING A BOUNTY ORPHANING ITS CALENDAR EVENT — KILLED 2026-08-01, NEVER REAL.**
- **THE SLATE PANEL/HEADER MISMATCH — KILLED 2026-08-01, NEVER REAL.**
- **DELETING THE STANDING-DUTIES BLURB — REVERSED BEFORE IT SHIPPED, 2026-08-01.** It is the section's EMPTY STATE.
- **THE OFFLINE SHELL — DEPRIORITIZED 2026-08-01, NOT KILLED.** **A caching bug cannot be fixed by pushing a fix.**
- **FIXING THE `verbLabel` ENUM LEAK — DECLINED 2026-08-01.**
- **TIGHTENING `wall_request_redemption` TO MATCH THE RLS POLICY — DECLINED 2026-07-31.** It would have meant only the household owner could ever redeem from the wall.
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.** The doc was wrong, not the build.
- **Marquee/scrolling titles — DECLINED.** · **Title `maxLength` — DECLINED.** **The container was the defect.** ⚠️ **The reasoning was re-derived and re-violated on 09-11 by a swipe carousel on the same surface.**
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.** ✅ **Disproven on a real month boundary 2026-08-01.** ⚠️ **The same-row replacement carried an unnoticed cost for six weeks: no lineage. Closed 09-11 by `series_id`.**
- **Ember tier heat on the Slate — SUPERSEDED.** · **A segment control for Slate/Ledger — SUPERSEDED.** · **A numeric collapse threshold on the Slate — DECLINED.**
- **The Ledger's record-vs-scrapbook fork — DISSOLVED, not decided.**
- **Capacitor — DECLINED**, with two named reopen triggers. · **Kid-auth — DECLINED.** · **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** Embers are the only currency of the platform economy.
- **"Layer" — retired.** · **Cinder and Holt — DECLINED. The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30; the rename LANDED 2026-07-31.**
- **"Parent" and "Kid" as user-facing role labels — SUPERSEDED 2026-08-03.** The pair is KEEPER and KIN. Identifiers unchanged. ⚠️ **The sweep searched "Parent" and "Kid" and missed four "adult" strings. See NOW.**

---

## 🟠 THE ROW PRIMITIVE — STILL OPEN, STILL SMALL

The Slate wraps titles correctly with no primitive at all, and the board always did. **The job shrank to deleting a declared `truncate` at three sites.**

⚠️ **IT GREW A LIST HALF ON 2026-09-11.** List rows truncate mid-line inside a container with room for three, and the add bar's section chip truncates to "NO SEC…". **Same disease, different surface.**

⚠️ **The wall remains the open half:** it is a fixed-height ambient rail, and wrapping there may push rows out of view where wrapping on a scrollable phone surface costs nothing. **Cap-at-two-lines may earn its keep on the wall and only on the wall. Visual success criterion — Scott's eyeball, not a brief.**

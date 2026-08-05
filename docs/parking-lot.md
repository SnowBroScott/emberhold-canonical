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

- **`ledger.tsx` and `slate.tsx` each carry their own `og:title`; the other 31 routes do not.** Not a defect. **Either 31 are missing it or 2 have extra.** Cheap either way; needs a call on which is right.
- **An application-level export routine.** Offered by Lovable during the 08-02 backup conversation. **Real idea, wrong time.** It is a build to solve a problem a support answer might solve for free. **Revisit only if Lovable comes back without a confirmed retention window.**
- **Should the reward audience enum get its own label map?** The Vault's `household` / `adults_only` is a separate enum from the quest audience and now has two hardcoded "Keepers only" strings. `AUDIENCE_LABEL` cannot cover it because the keys differ. **Raised by the Code agent on 08-03 as a suggestion, deliberately not built. Two sites is not yet drift.**

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**The rule, established 2026-07-31: DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**The second rule, established 2026-08-01: NO EM DASHES IN USER-FACING COPY — and none in the briefs handed downstream, because the second is how the first happens.** ⚠️ **En dashes in time and date ranges are typographic convention, not the tell.**

**The third rule, established 2026-08-03: VERTICAL HEIGHT ON A SCROLLING PHONE BOARD IS ITS OWN ARGUMENT.** The "Available to anyone" line was deleted on the copy rule and on the height rule independently, and the height rule is the one Scott reached for. **Six bounty cards on a screen, four carrying a wasted line, is a reason all by itself.**

**The disease it treats:** design-rationale phrasing from jAIne's build briefs gets rendered as UI copy. **Punctuation escapes the same way the reasoning does.**

**The comparison point for what GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to a Keeper"* and the wall's *"A KEEPER APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the approval gate, which is invisible and is the thing people get wrong.

⚠️ **TOUCHED IS NOT REVIEWED.** The Keeper and Kin rename swapped a word on eleven screens. **None of them got a voice read.** A screen marked unreviewed below is still unreviewed even if the rename edited it.

**Batch before firing.**

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31, closed 08-02** | ⚠️ *Its EditPanel picker was relabeled 08-03. Copy still current.* |
| **The Ledger** | ✅ **Reviewed 07-31** | |
| **Auth / sign-in** | ✅ **Reviewed 08-01** | ⚠️ *Rename touched six strings 08-03 including the marketing panel a stranger reads first. Worth a re-read on that panel only.* |
| **Campaigns** | ✅ **Reviewed 08-01** | ⚠️ *Empty state reworded by the rename.* |
| **Calendar** | ✅ **Reviewed 08-01** | Day empty state → **"The day is clear."** |
| **Briefing / Hub** | ✅ **Reviewed 08-01** | ⚠️ *The FAB overlapping the Campaigns progress bar is layout, not copy.* |
| Board | ⬜ unreviewed | ⚠️ **Two headings changed 08-03. `board.tsx:149`'s kicker may now stutter against the `<h1>` below it. Scott's eye.** |
| Vault | ⬜ unreviewed | ⚠️ **Seven strings changed 08-03, the largest single surface in the rename.** ⚠️ **`vault.tsx:820` is a DEFECT, not copy. See NOW.** |
| Lists | ⬜ unreviewed | ⚠️ The "5 OPEN · 348 DONE" fossil counter is a separate defect, not copy. |
| Ranks / leaderboard | ⬜ unreviewed | |
| You / profile | ⬜ unreviewed | ⚠️ *Six strings changed 08-03.* |
| Create / edit | ⬜ unreviewed | ⚠️ *Audience picker relabeled 08-03.* |
| Bounty detail (`quest.$id`) | ⬜ unreviewed | ⚠️ *Two strings changed 08-03.* |
| The wall | ⬜ unreviewed | ⚠️ **The only semi-public surface. Ranks above the private screens.** ⚠️ **Nine strings changed 08-03 and none verified on the glass.** ⚠️ **It holds independent copies of strings that also live in `Briefing.tsx`; read them as a pair.** |
| First run — adult (6 screens) | ⬜ unreviewed | ⚠️ **Heavily rewritten by the em-dash sweep; worth a voice read.** |
| First run — kid / joiner (3 screens) | ⬜ unreviewed | ⚠️ **The 08-02 recon found NO role literals here, which is either true or a search miss. One look on the glass settles it.** |
| Onboarding — add family | ⬜ unreviewed | ⚠️ **Still says "this adult" in the PipSpark body, one line below a label reading Keeper PIN. See NOW.** |
| Onboarding — first bounty | ⬜ unreviewed | ⚠️ **High-stakes screen; read it whole.** |
| Notifications | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | **Deliberate debug surface, kept until the pre-production sweep.** ⚠️ *`quest-log.tsx` was edited 08-03 anyway because its gate copy and its picker both said "adult".* |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🟠 DOES THE ACTIVITY FEED'S RESOLVED NAME STAY FROZEN?** The activity label decision is settled in shape: **the feed stores facts, never display text**, splitting into `actor_id` (who clicked) and a validated `subject_profile_id` (who gets credit), with names resolving at render time. **What is NOT settled is whether the resolved name is frozen at write time or looked up live.** The current schema comment reads "frozen display name" and that is a deliberate choice, not an accident. **If Mia becomes Amelia, does history say Mia?** Frozen is defensible and arguably correct. ⚠️ **This blocks the backfill design, not the schema shape.**
- **🟠 IS `kids-only` A DEAD AUDIENCE VALUE?** Scott is ~92% confident no bounty in his hold has ever used it. ⚠️ **The reason is structural and the rename exposed it: "Kids only" read as a rule a Keeper sets; "Kin only" reads as a description of who is around. The tag was borrowing authority from the word "kid".** **Keepers only excludes a Kin from something they should not have. Kin only excludes a Keeper from something they can obviously do, in a household where the Keeper wants the work done.** **One grouped read of `quests.audience` across all thirteen holds settles it.** If empty, killing the value is a cheap migration on empty data and belongs in the Phase 2 pre-production sweep, taking the badge with it. **Do not kill it on 92%.**
- **🟠 DOES THE WALL WANT AN AUDIENCE BADGE?** `wall.tsx`'s `BountiesList` shows no restriction indicator for any bounty; the phone's `QuestCard` does. **Cross-surface asymmetry on the only semi-public surface.** ⚠️ **Not obviously a defect: the wall is an ambient rail with different density constraints, and a restricted bounty on a shared display may not need the same treatment as one on a phone.** **Scott's eye.**
- **EMPTY ROSTER SEAT — auto-default an avatar, or leave it an "unclaimed, tap to pick" seat?** ✅ **The unknown is resolved as of 08-03: the picker IS offered during add-member and CAN be skipped, leaving a themed empty circle rather than a broken render.** **So this is a nudge, not a fix, and it is polish rather than a default-path defect.** **jAIne's lean: do NOT auto-default and do NOT guess. Make the empty seat tappable, routing to the picker.** The roster is the one place a hearthmate says "this is me." **The one exception is the wall**, where a silhouette is a dead spot on an ambient display. **Raised 2026-07-29, informed 2026-08-03, still unratified.**
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
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.** **Phase 2, with the avatar free/paid split.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the Kin or Keeper-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott has not called it.
- **Unify `quest.audience` and `reward.audience`?** Two parallel flags, deliberately not unified. ⚠️ **08-03 raised the stakes slightly: they now carry near-identical display labels from two different sources.** A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 GLASS-VERIFY THE THREE RENAME COMMITS.** `9c83fc4`, `e8223f4`, `f9f731e`. **The Vault as a Keeper and as a Kin, the Board, quest detail, and the wall.** ⚠️ **The Vault took seven strings and the wall took nine; neither has been looked at.** **Free, and it is the difference between three commits and three shipped changes.**
- **🔴 BACKUP: TRIGGER THE EXPORT.** **Cloud → Advanced settings → Export data.** Free, self-service, no credits. ⚠️ **Lovable could not confirm PITR, schedule or retention. Zero backups to one backup is the largest single risk reduction available on this board.** Then ask a human at Lovable for the retention window.
- **🔴 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper, never Fully Kiosk.** If Chrome withholds it, remote-debug over USB: **Chrome's Manifest panel names the failing criterion in plain text.**
- **⚠️ `vault.tsx:820` TELLS A KEEPER TO ASK A KEEPER.** `KidCuratedVault`'s empty state is reused for both the Kin view and the Keeper's own "Your store" with no role prop, so a Keeper looking at their own empty store is told to **ask a Keeper to stock the vault**. **A real defect, found by the 08-03 recon, deliberately left in place because it is behavior rather than words.** Frontend-only. Needs a prop or a conditional, and needs Scott to say what the Keeper's version should read.
- **`onboarding.add-family.tsx` STILL SAYS "this adult"** in the PipSpark body one line below a label that now reads Keeper PIN. **A real recon hit that jAIne dropped from the brief; the agent correctly refused to fix what it was not asked to fix and reported the overreach it had caught in itself.** **Rides along on the next frontend job.**
- **🖊️ THE SCREEN COPY PASS.** Six screens reviewed, eleven touched by the rename without being reviewed. **The wall ranks first.**
- **`board.tsx:149`'s kicker may now stutter** against the `<h1>` directly below it, since both read "Open bounties". **Delete the kicker if it reads badly. Scott's eye.**
- **`Testing retired` stays retired** once its successor's date arrives. One look.
- **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`** — a failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- **Two derivations of role** — `useActiveMember().role` vs `has_role(auth.uid())`. ⚠️ **The fork decision makes this permanent by design rather than pending a fix.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward; verify before building.**
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- **The Briefing's FAB overlaps the Campaigns progress bar.** **Layout, Scott's eye.**
- **Prod test-object cleanup, three objects.** `Testing redemption tracking`, `Testing retired`, `testing approve`. ⚠️ **`testing approve` minted 10 real embers to Mia and deleting the bounty will not unwind them.** **The open question underneath: every glass walk that touches the economy puts real currency into a real kid's balance. Do test mints get cleaned up, or accepted as the cost of verification?**
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Grant-revoke verification probe job.** Drafted, deferred ten times. **`SQLSTATE 42501` = FAIL, any application-level error = PASS.**
- **The `quests.audience` grouped read.** Settles whether `kids-only` is dead. **Should ride along on the next Code job rather than justifying its own.**
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15 to 20 min), screenshot from the phone.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

---

## NEXT (soon — off the critical path)

### Toolchain

- **⚠️ BUN IS THE PACKAGE MANAGER. NEVER npm OR yarn.** **Name this line in every Code brief, exactly like `routeTree.gen.ts`.** ✅ **Held across four more jobs on 08-03.**
- **🔵 `bun audit` BASELINE BANKED 2026-08-02: ten findings, all dev-tree, none shipping.** Re-run after any dependency change; ignore otherwise.
- **🔵 THE 47 TANSTACK TYPECHECK ERRORS — REAL, AND DELIBERATELY NOT FIXED.** One class: a missing `search` property on navigate, redirect and Link calls. **Type layer only, zero runtime impact.** ✅ **NAMING THE NUMBER IN THE BRIEF TURNS A POLLUTED OUTPUT INTO A CONTROL.** Three 08-03 jobs reported cleanly against it. **Revisit only if a router upgrade forces it.**
- **⚠️ `routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** **Name it in every brief.** Held across thirteen jobs.
- **⚠️ `query_quest.mjs` is still untracked in the working tree.** Named in every 08-03 brief and correctly left alone each time.
- **Claude Code's "sync before reading" rule needs a forcing function.** ✅ **Worked thirteen times. Keep the line in every brief.**
- **`wall_request_redemption` IS CALLED FROM THE VAULT AND ITS NAME LIES.** Deliberate debt; renaming an RPC is a migration. **Rename when something else takes that function to Lovable.**
- **⚠️ PLAN-MODE ITERATIONS ARE BILLED.** **Plan mode stays the right call for migrations, because a code revert is not a database revert, but the review is ONE pass.**

### The activity log rework

*Design settled 08-03. Blocked on credits and one open question, not on thinking.*

1. **`subject_profile_id` added and validated**, `actor_id` keeps meaning who clicked. **Four of eight write sites currently conflate them.**
2. **`logActivity` moves server-side.** ✅ **Not a proposal: `admit_pending_member` and `deny_pending_member` already INSERT from inside the SQL function and are the only two sites where the label is server-verified.** **The pattern is shipped twice and working.**
3. **The backfill.** ⚠️ **Blocked on the frozen-name question in OPEN DECISIONS.**
4. ⚠️ **Canon claims three verb switches exist; the 08-03 recon found two.** `hearth-log.tsx` and `NotificationBell.tsx`. **One curl to settle before anything is built on the assumption.**

### Onboarding, phase three

- **The kid joiner flow.** 🔵 **DOWNGRADED 2026-08-02, Scott's call.** Adult-supervised at the exact moment it runs. **Will surface itself. Not a blocker.**
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read.** ⚠️ **Part of the screen copy pass.**
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### The Forge — Option A, household-scoped

*Blocks nothing. ~$192/yr of cost avoidance. **Design is a rest-period activity; building waits for Phase 1.** Option A's shape lives in `master-spec.md` Part II.*

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

- **Vault favorites → real per-profile persistence** (currently `localStorage`, so they do not follow a Kin to another device).
- **Quality — a rating with no consumer.**
- **Re-forge reach across the 13.**
- **The offline shell / app-shell cache — DEPRIORITIZED, see LATER.**
- **Ghost successor cleanup.**
- Quick Add default EXPANDED on empty board · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar event-pill member color · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

**The offline shell / app-shell precache** (deliberately deferred 08-01; reopens with PWA push, same mechanism) · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · photo avatars · kid-vs-kid impersonation · favorites on the wall · the timezone nudge · the "how Scott & jAIne work" collaboration profile · an application-level export routine.

**⚠️ FLAT / PEER HOLDS IS NO LONGER JUST BACKLOG. It is the single named reopen trigger for the own-session fork.** Roommates, a nanny, a non-custodial parent. **The walk-up boundary is only sound because physical possession of the device equals household authority, which is true of a family and false of roommates.** Building it reopens per-member auth, kid-auth, and a harder COPPA posture, all at once. **It is not a feature request. It is an architecture change wearing one.**

### PHASE 2 — money and paperwork

**Stripe · the Founding Guildhall build (checkout, webhook, entitlement write) · refund posture · tax posture · COPPA · the privacy policy (which must name `flock.js`) · the landing page · the Pip-guided install tutorial · PostHog.**

⚠️ **THIS WAS ONE ROW ON THE CRITICAL PATH AND IT IS AT LEAST FIFTEEN.** **The decomposition session is itself a task, costs zero credits, and would tell us within an hour whether Phase 2 is three sessions or ten.**

**⭐ THE AVATAR FREE/PAID SPLIT MOVED HERE 2026-08-03.** All 48 avatars are live, pickable and rendering. **The 16-open / 32-locked split is the mechanism for a Phase 2 SKU and belongs with the SKU.** ⚠️ **Building it now means shipping locks on 32 faces with nothing to unlock them with, in front of thirteen live households.** **It is a locked door in a house with no key cut yet.** The founder tier-tag verification (flip on, confirm 32/16, flip off) travels with it.

⚠️ **`system_flags` PRECONDITION.** The read policy is `TO authenticated USING (true)` and nothing constrains a bare key/value store. **Before the first non-public or non-boolean flag lands there — a Stripe mode switch, a rollout percentage, a kill switch — the read policy must be narrowed.** Harmless today because the one row is a global `false`.

### Pre-production sweep

**`/quest-log` and `/hearth-log` route deletion** joins **prod test-object cleanup** on this shelf. **Not deletion-pending. Scheduled.** ⚠️ **Killing the `kids-only` audience value joins them if the grouped read comes back empty.**

### Forge at stranger scale — what does NOT come along

Injury-prescription liability posture · commercial-gym equipment model. **Both are stranger-scale problems and Forge is household-scoped.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.**

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **PER-MEMBER AUTH — DECLINED 2026-08-03.** Rejected on four grounds. **(1)** Kids have no email, so it requires building a credential path, and `kid-auth` was already DECLINED, making this a reopen of a settled call. **(2) The wall does not benefit and cannot**: it is one device any member walks up to, so `wall_request_redemption` and the validated-actor-id pattern survive unchanged. **It adds a second authority model rather than replacing the first.** **(3)** It creates accounts for under-thirteens, which is the literal COPPA trigger, making a Phase 2 item that is already fifteen rows more expensive. **(4)** Switching becomes sign out and sign in on the shared family device, degrading the daily loop. **REOPENS ONLY ON FLAT OR PEER HOLDS.**
- **"KIDS ONLY WAS ALWAYS A LIE" — jAINE'S ARGUMENT, WRONG, KILLED 2026-08-03.** jAIne argued the audience flag never excluded adults, so "Kids only" was a copy defect the rename had caught. **False. The audience field has THREE values and `kids-only` is a real restriction that does exclude Keepers.** The stop-clause caught it before it shipped. ⚠️ **The brief had been written from a recon inventory that listed the label without listing the field's value set. jAIne never asked what the values were.**
- **"AVAILABLE TO ANYONE" — DELETED 2026-08-03.** ⚠️ **It never read `quest.audience` at all.** It rendered whenever there was no assignee, **so a `parents-only` bounty could show the Keepers only badge and a line claiming it was available to anyone, on the same card.** Deleted on two independent arguments: it explained something the card already showed, and it cost a line of height on every unrestricted card on a scrolling board. **The height argument was Scott's and it is the stronger one.**
- **"THE Parent/Keeper RENAME WOULD SEAM THE FEED HISTORY" — jAINE'S ARGUMENT, WRONG, KILLED 2026-08-03.** jAIne argued that storing display text in the activity log would force a migration across all history or leave a permanent Parent/Keeper seam. **`actor_label` stores member NAMES, not role words.** The rename touched zero stored rows. **The argument that survives is the two-meanings problem, and the recon proved it rather than the reasoning.**
- **THE ONBOARDING ADD-MEMBER AVATAR RECON — DEAD 2026-08-03, ANSWERED BY SCOTT.** Canon claimed a member added during onboarding could not choose an avatar, and inferred every non-creator starts null by construction. **The picker is offered and can be skipped, leaving a themed empty circle.** **Not a default-path defect. The empty-roster-seat decision absorbs what is left.**
- **AVATAR ROSTER TRANSPORT AS OUTSTANDING WORK — CORRECTED 2026-08-03.** All 48 upload, slot and render, everywhere including the wall. **It sat on the critical path as Gate A's tail for weeks while it was working.** ⚠️ **jAIne read the manifest's "READY, not yet uploaded" and reasoned from it instead of asking the person who uses the app daily.**
- **THE 47 TYPECHECK ERRORS AS AN npm ARTIFACT — DISPROVED 2026-08-02.** Identical count under npm's 1.170.17 and bun's 1.170.16.
- **"ADULT PIN STORED IN PLAINTEXT IN localStorage DURING SIGNUP" — DISPROVED 2026-08-02.** Only two localStorage keys exist and neither is PIN-related. ⚠️ **A stale scanner entry is worse than a wrong one.**
- **REVOKING `sandbox_exec`'s EXECUTE GRANTS — DECLINED 2026-08-02.** Platform-managed. **Narrowing is a Lovable support conversation, never a manual revoke.**
- **`approve_quest()` AS A NEW RPC WITH FIVE REWRITTEN CALL SITES — DECLINED 2026-08-02.** **A trigger validates the actor at all five sites at once with zero client changes.**
- **DELETING A BOUNTY ORPHANING ITS CALENDAR EVENT — KILLED 2026-08-01, NEVER REAL.**
- **THE SLATE PANEL/HEADER MISMATCH — KILLED 2026-08-01, NEVER REAL.** **A label in a screenshot is not necessarily on the surface at all.**
- **DELETING THE STANDING-DUTIES BLURB — REVERSED BEFORE IT SHIPPED, 2026-08-01.** It is the section's EMPTY STATE. **The delete-don't-reword default has an exception and this is it.**
- **THE OFFLINE SHELL — DEPRIORITIZED 2026-08-01, NOT KILLED.** **The cache strategy carries all of the risk for almost none of the benefit**, and a caching bug cannot be fixed by pushing a fix.
- **DELETING `/quest-log` AND `/hearth-log` "ONCE THE SLATE IS TRUSTED" — SUPERSEDED 2026-08-01.** **Rescheduled to the pre-production sweep.**
- **FIXING THE `verbLabel` ENUM LEAK — DECLINED 2026-08-01.**
- **"FOUR INDEPENDENT VERB→DISPLAY IMPLEMENTATIONS" AS A DIVERGENCE PROBLEM — DOWNGRADED 2026-08-01.** ⚠️ **`wall.tsx`'s `event_created` omits "to the calendar."** ⚠️ **And the 08-03 recon found only TWO renderers, not three. Unresolved.**
- **A BRIGHT-HEAVY SLATE AS A PROBLEM — RESOLVED 2026-07-31.**
- **TIGHTENING `wall_request_redemption` TO MATCH THE RLS POLICY — DECLINED 2026-07-31.** It would have meant **only the household owner could ever redeem from the wall.**
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.** The doc was wrong, not the build.
- **Marquee/scrolling titles — DECLINED.** · **Title `maxLength` — DECLINED.** **The container was the defect.**
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.** ✅ **Disproven on a real month boundary 2026-08-01.**
- **Ember tier heat on the Slate — SUPERSEDED.** · **A segment control for Slate/Ledger — SUPERSEDED.** · **A numeric collapse threshold on the Slate — DECLINED.**
- **The Ledger's record-vs-scrapbook fork — DISSOLVED, not decided.**
- **Capacitor — DECLINED**, with two named reopen triggers. · **Kid-auth — DECLINED.** · **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** Embers are the only currency of the platform economy.
- **"Layer" — retired.** · **Cinder and Holt — DECLINED. The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30; the rename LANDED 2026-07-31.**
- **"Parent" and "Kid" as user-facing role labels — SUPERSEDED 2026-08-03. The pair is KEEPER and KIN, and it landed the same day.** Identifiers unchanged.

---

## 🟠 THE ROW PRIMITIVE — STILL OPEN, STILL SMALL

The Slate wraps titles correctly with no primitive at all, and the board always did. **The job shrank to deleting a declared `truncate` at three sites.** ⚠️ **The wall is the open half:** it is a fixed-height ambient rail, and wrapping there may push rows out of view where wrapping on a scrollable phone surface costs nothing. **Cap-at-two-lines may earn its keep on the wall and only on the wall. Visual success criterion — Scott's eyeball, not a brief.**

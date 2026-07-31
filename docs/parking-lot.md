# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-30 night)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ IS A BRIGHT-HEAVY SLATE HONEST OR OPPRESSIVE? — NEW, AND IT NEEDS A WEEK OF LOOKING, NOT AN ANSWER TONIGHT.** Roll-forward moved every stale weekly to the current anchor, so the Slate opened at **`13 need doing`** — all correct, one row per duty, no pile. **But this is the first look at what the rule actually produces on a lived-in hold.** The design premise was *a dim Slate is a well-kept hold*; the first render is mostly bright. **Do not touch it reactively.** Live with it a week. If it reads as pressure rather than honesty, the lever is grouping or a softer "due now" treatment, **not** re-hiding work.
- **🟠 THE ROW PRIMITIVE'S REMAINING SCOPE — the wall specifically.** The Slate wraps titles correctly with no primitive at all, and the board always did. **So the job shrank to deleting a declared `truncate` at three sites.** ⚠️ **The wall is the open half:** it is a fixed-height ambient rail, and wrapping there may push rows out of view where wrapping on a scrollable phone surface costs nothing. **Cap-at-two-lines may earn its keep on the wall and only on the wall. Visual success criterion — Scott's eyeball, not a brief.**
- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller.** The column does two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels. **(b) is jAIne's lean — same shape as the marker's `profile_id` fix.** Design call, not a bug fix.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.**
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
- **Module navigation.** Seven tabs is one past mobile comfort. ✅ **The Slate/Ledger half is ANSWERED and shipped — no tab added, Briefing card swap.** **Forge's contained-route posture still defers the module half.**
- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. **Best argument: eight tables and two functions were created in production by accident.**
- **⚠️ Backend ownership + data backup.** Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker.
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** **Waiting on concrete examples from Scott.**
- **The founder paywall flip — timing only, mechanism is built.** ⚠️ **The grandfather write must run as `service_role`.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **🔴 THE MARKER'S READ AND WRITE, AS ONE CHANGE.** ⚠️ **The two bugs cancel — fixing the read alone gives an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, **Lovable lane, needs credits.** **Blocks the kid joiner flow on shared devices.**
- **🔴 `master-spec.md` FULL REGENERATION — NOW UNBLOCKED.** The sequencing condition (*after the sweep lands*) is met. **Six of tonight's decisions override its current lines**, including a pinned ⚠️ that tonight superseded. **jAIne read ~200 of 669 lines this session and declined to fake a regeneration.** Dedicated session, first in queue.
- **🔴 REPO-WIDE SWEEP-COVERAGE GREP — free, Code lane.** The sweep table listed `quest.$id.tsx` as swept having changed only the head title; **two user-facing strings remained.** **The rename is not landed until a grep says so.** The 07-11 precedent is exactly a sweep that self-reported clean.
- **🔴 THE THREE UNEXERCISED GUARANTEES — all free, all glass.**
  - **Retire a duty** → lands dim in Retired, Delete gone, filtered on `retired_at`.
  - **Turn in a bounty and don't approve it** → mount the board → confirm it did not roll. *(No submitted rows exist in the hold; this guarantee has zero evidence.)*
  - **`/create?recurring=true`** from the Slate's empty state → opens with a cadence on.
- **⚠️ WATCH THE BOARD ON AUGUST 1.** Every monthly successor is dated 2026-08-01. **This tests successor arithmetic and board clutter. It does NOT test roll-forward** — the monthly roll branch has no natural test until **September 1**. Do not conflate them.
- **🟠 THE ROW PRIMITIVE — RE-SCOPED SMALLER.** Delete the declared `truncate` at the three confirmed sites. **The wall's treatment is an open decision above.** Free, Code lane, isolated push.
- **Delete `/quest-log` and `/hearth-log`** once the Slate is trusted on the glass. Their copy is deliberately unswept until then.
- **Feed verb history check.** If old activity rows stored their verb string, history says "quest" and new entries say "bounty" — **mixed vocabulary, the disease the rename killed.** Scroll the feed.
- **Recurrence chip consistency.** It reads `Monthly · 1st` / `Weekly · Mon` on the Slate. **Confirm the board and create/edit agree, or the surfaces contradict each other.**
- **Two derivations of role** — `profiles.role` in `FirstRunGate`, `user_roles` in `useMyProfile()`. **The `isActiveQuest` divergence pattern, second occurrence.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building anything separate.**
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card advertising "standing duties & open bounties." Cosmetic.
- **Prod test-object cleanup — PROMOTED.** `Testing redemption tracking` is now visible to users in the Ledger. It stopped being housekeeping and started being product surface.
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Grant-revoke verification probe job.** Drafted, deferred six times.
- **The floor — avatar render fallback.** **Check the wall.**
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **The Vault-page approval path, post-migration.** Single adult, approve a pending kid redemption from the Vault page. Cheap.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

---

## NEXT (soon — off the critical path)

### The rename's tail

- **`north-star.md` string inheritance.** Two lines pick up the rename: the membrane line ("the game lives in quests only") and the one-line description. **Cosmetic inheritance — do not regenerate the doc for it.** Fold it into the next north-star touch.
- **The Haiku vocabulary sweep still has two targets:** `Feast`→`Hall` and user-facing `points`→embers. ✅ **`Quest`→`Bounty` is done** *(pending the coverage grep)*. **The `/first-run/adult/approving` `points` violation was fixed with the Bounty sweep** — check whether other `points` instances survived.

### Onboarding, phase three

- **Walk the kid joiner flow with an actual kid.** Three beats to the adult's five, built and published, never exercised. **Blocked on the shared-device path until the marker read/write pair lands.**
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read now that it's live and renamed.**
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **`member_admitted` renders as `"Mom · Leo"`.** One case in two switches.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Toolchain

- **⚠️ `routeTree.gen.ts` DRIFT IS CONFIRMED LIVE, NOT SUSPECTED.** Tonight's build regenerated it with boilerplate and Code discarded it to keep the commit scoped. **Correct call — but the generated and committed files disagree, every build surfaces it, and every agent has to know to throw it away.** ⚠️ **Promoted from "probably self-healing" — it now has a demonstrated failure mode, not a theoretical one.**
- **`progression.test.ts` has a pre-existing `tsc --noEmit` error** — `vitest` types missing. Unrelated to any session's work, surfaced by tonight's build check. **Clean it with the `progression.ts` job.**
- **Claude Code's "sync before reading" rule needs a forcing function.** ✅ **Worked tonight** — Code reported being behind, pulled, and reported the hash before reading. **Keep the line in every Code brief.**

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
- **Ghost successor cleanup.** **Feed verb drift.**
- Quick Add default EXPANDED on empty board · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar event-pill member color · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · flat/peer holds · photo avatars · kid-vs-kid impersonation · role-label retirement ("Parent/Kid") · favorites on the wall · the timezone nudge · the "how Scott & jAIne work" collaboration profile.

### Forge at stranger scale — what does NOT come along

Injury-prescription liability posture · commercial-gym equipment model. **Both are stranger-scale problems and Forge is household-scoped.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.** A retained household becomes worth $25 + $10 + $5 instead of $25 flat.

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **Marquee/scrolling titles — DECLINED.** Six simultaneously scrolling titles on a wall is a slot machine.
- **Title `maxLength` — DECLINED.** jAIne's proposal, killed by the photos: the truncating titles were 14 and 20 characters. **The container was the defect.**
- **The ADULTS ONLY badge as the truncation culprit — DISPROVEN.** `truncate` is applied unconditionally; `overflow-hidden` zeroes the title's flex auto-min-width and it absorbs the squeeze **by CSS default.** The badge narrows space and makes it trigger sooner; it does not cause it.
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED, 2026-07-30 night.** Two independently-authored surfaces (board, Slate) wrap correctly with no primitive. **The job is deleting a class at three sites.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.** The intuitive implementation produces the guilt pile the rule exists to prevent.
- **Ember tier heat on the Slate — SUPERSEDED, 2026-07-30 night.** Brightness on the Slate means state, full stop.
- **A segment control for Slate/Ledger — SUPERSEDED same session.** Symmetric tabs imply equal weight and would have lied. Secondary link is honest.
- **The Ledger's record-vs-scrapbook fork — DISSOLVED, not decided.** Same rows, different reader. Role-aware depth is a filter, never a second system.
- **A numeric collapse threshold on the Slate — DECLINED.** Collapse by state; it self-sizes.
- **Capacitor — DECLINED**, with two named reopen triggers: PWA push proving load-bearing at Gate E, or an appliance play after Gate E returns retention driven by the wall.
- **Kid-auth — DECLINED.**
- **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** Embers are the only currency of the platform economy.
- **"Layer" — retired.** Three of four proposed functional areas decomposed entirely into quests, lists and campaigns. They were registers all along.
- **Cinder and Holt — DECLINED.** Fine names attached to a character that already has one. **The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30, and the sweep landed the same night.**

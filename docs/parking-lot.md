# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

**THE SCREEN COPY PASS** is its own running section below. It is a review inventory, not a backlog item — it tracks which screens have been read for voice and which haven't.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-31)*

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**The rule, established 2026-07-31: DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**The disease it treats:** design-rationale phrasing from jAIne's build briefs gets rendered by Lovable as UI copy. The app ends up explaining its own architecture to a nine-year-old. *"Everything with a future," "recurring duties — one line, forever," "here until they're approved"* were all brief text, verbatim, on the glass.

**The comparison point for what GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to an adult"* and the wall's *"AN ADULT APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the approval gate, which is invisible and is the thing people get wrong.

**Batch before firing.** Four strings is not worth a Code job on its own; ride the next frontend commit or accumulate a few screens.

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31** | Eyebrow → EVERYTHING THAT'S LIVE. Standing + One-offs subtitles deleted. Ledger link reworded. |
| **The Ledger** | ✅ **Reviewed 07-31** | Own copy already clean; inherits the Slate's link. |
| Board | ⬜ unreviewed | |
| Vault | ⬜ unreviewed | ⚠️ *"Each request goes to an adult"* is a KEEP — the model for good subtitle copy. |
| Briefing / Hub | ⬜ unreviewed | |
| Campaigns | ⬜ unreviewed | |
| Calendar | ⬜ unreviewed | |
| Lists | ⬜ unreviewed | ⚠️ The "5 OPEN · 348 DONE" fossil counter is a separate defect, not copy. |
| Ranks / leaderboard | ⬜ unreviewed | |
| You / profile | ⬜ unreviewed | |
| Create / edit | ⬜ unreviewed | |
| Bounty detail (`quest.$id`) | ⬜ unreviewed | |
| The wall | ⬜ unreviewed | ⚠️ The only semi-public surface. Ranks above the private screens. |
| First run — adult (6 screens) | ⬜ unreviewed | ⚠️ Flagged 07-30 as deserving a second read now that it's live and renamed. |
| First run — kid / joiner (3 screens) | ⬜ unreviewed | |
| Onboarding — first bounty | ⬜ unreviewed | ⚠️ Was completely unswept until 07-31. High-stakes screen; read it whole. |
| Notifications | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | Deliberately unswept rollback path. Slated for deletion. |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🔴 WHAT DOES `actor_label` MEAN?** `actor_id` is server-derived and truthful; only the display string is client-supplied. **But four of six call sites pass a label that is deliberately NOT the caller.** The column does two jobs: sometimes "who clicked," sometimes "who gets credit." Options: (a) leave it and accept a rendered-name forgery vector under the walk-up boundary, (b) add a validated `subject_profile_id` and derive the label server-side, (c) split into two labels. **(b) is jAIne's lean — same shape as the marker's `profile_id` fix.** Design call, not a bug fix.
- **⚠️ Own-session vs per-member-auth — more riding on it than it looks.** **Four open items wait on this one call:** the `adults_only` rewards audience read, the `parents_only` quest details read, the ignored "adult PIN lock isn't tied to real permission checks" finding, and the ignored "any member can submit a redemption attributed to another member" finding. **Items 3 and 4 are marked Ignored in the Lovable dashboard and that panel will never tell you they're conditional.** ⚠️ **07-31 gave this a mechanism: `useActiveMember().role` reads the switched-to profile client-side, while every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is. Every "active member" feature stands on that disagreement.**
- **🟠 SHOULD `logActivity` MOVE SERVER-SIDE? — NEW 07-31.** It is a client-side call bolted onto each call site by hand, so every new site can forget to make it — and one did, silently, for the entire life of the wall's approve path. **The mechanism fix is the definer RPC writing its own log row.** Cost: a migration, and the RPC has to know the display label. **Against: four surfaces already render their own lines from the verb enum, so the label may not belong in the DB at all.** Related: the four-implementations problem below.
- **🟠 IS `parent_self_redeem` SKIPPING THE APPROVAL GATE CORRECT? — NEW 07-31.** It inserts `status='approved'` with `decided_by = auth.uid()` in one call. An adult redeeming their own embers is requester and approver simultaneously. **Reads as deliberate in the code and it has never been written down.** Ratify it or change it, but stop having it be an undocumented behavior of the economy.
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

- **🔴 THE MARKER'S READ AND WRITE, AS ONE CHANGE.** ⚠️ **The two bugs cancel — fixing the read alone gives an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, **Lovable lane, needs credits.** **Blocks the kid joiner flow on shared devices.** ⚠️ **07-31's Vault bug was the same class and the same root cause — a profile id compared against an auth id. Second confirmed instance.**
- **✅ `master-spec.md` — CORRECTED 2026-07-31, FLAG CLOSED.** ⚠️ **The item itself was stale: it described a 669-line doc owing a fold, written before the 07-30 (late) fold landed and never revised. Scott caught it.** The real defect was the inverse — nine lines describing decided, shipped things as open, including the Ledger's *"ITS SHAPE IS NOT DECIDED / do not build from this section"* while the Ledger was live. All nine corrected. **Residual, non-urgent: ~400 lines (Part II/Forge, onboarding, schema detail) were not re-read on 07-31. Believed current from the 07-29/07-30 folds. A full cold read is worth doing eventually.**
- **🟠 THE `verbLabel()` ENUM LEAK — CHECK THE THREE SURFACES THAT STAY.** `hearth-log.tsx` renders `QUEST APPROVED` because `verbLabel()` special-cases only `bounty_posted` and falls through to `verb.replace("_"," ")` for everything else. **Grep cannot find this — no line spells "quest."** `hearth-log` is the protected rollback path and is slated for deletion, so **the real question is whether wall.tsx, Briefing.tsx and NotificationBell.tsx carry the same fallback.** Code's read says their renderers already output "completed." **One cheap confirmation, and deleting `hearth-log.tsx` moots the rest.**
- **🟠 FOUR INDEPENDENT "VERB → DISPLAY LINE" IMPLEMENTATIONS.** `wall.tsx` (`tickerLine`/`tickerIcon`), `Briefing.tsx` (`pulseLine`/`pulseIcon`), `NotificationBell.tsx` (`lineFor`/`iconFor`), `hearth-log.tsx` (`lineFor`/`iconFor`/`verbLabel`). **Third occurrence of the divergence class, after `isActiveQuest` and the two derivations of role. It is exactly why a rename lands in three places and misses the fourth.**
- **⚠️ THE AUGUST 1 BOARD — THE CONTROL SNAPSHOT IS BANKED.** As of 07-31 07:22 under Monthly: **Mop Downstairs (Due now, Cade) · Vaccuum Downstairs (Due now, Cade) · Vacuum Upstairs (Due now, unclaimed) · Brush Chaos (Current, next Aug 1) · Change sheets (Current, next Aug 1).** Tomorrow all five read Aug 1 and look identical — **the snapshot is the only thing that tells rolling from spawning.** ⚠️ **The claim on Cade is the discriminator.** ⚠️ **Do NOT log this as the monthly roll branch's verification; the clean natural test is 2026-09-01.**
- **🔴 `submitted` DOES NOT ROLL — the obvious test does not work.** Turning in a bounty and watching it not move today proves nothing: it is due today, today is the anchor, there is nowhere to move. **Create a throwaway DAILY, claim it, submit it, don't approve it, read it the next morning.** Pass = still at yesterday's date, still submitted. Then delete it.
- **🖊️ THE SCREEN COPY PASS — see the section above.** Slate ✅ Ledger ✅.
- **Delete `/quest-log` and `/hearth-log`** once the Slate is trusted. ⚠️ **This moots the `verbLabel` bug for free — but check the other three surfaces first.**
- **`Testing retired` stays retired** once its successor's date arrives. One look.
- **The Slate detail panel read IN PROGRESS while the row header read "Done today."** Panel and header may render off different instances. Noticed 07-31, not chased.
- **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`** — a failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- **Deleting a bounty may orphan its calendar event.** Creating one writes an `EVENT CREATED` row; the deleted `Testing retired` was never checked against the calendar. **One look.**
- **Recurrence chip consistency.** Reads `Monthly · 1st` on the Slate. **Confirm the board and create/edit agree.**
- **Two derivations of role** — `profiles.role` in `FirstRunGate`, `user_roles` in `useMyProfile()`. ⚠️ **And a third split confirmed 07-31: `useActiveMember().role` vs `has_role(auth.uid())`.**
- **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building.**
- **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- **Prod test-object cleanup — PROMOTED AGAIN.** `Testing redemption tracking` is in the Ledger; `Testing retired` is in the Slate's Retired section, **and canon records no un-retire affordance.**
- **Signup glass checks #2 and #3.** Cold join-path signup; original-tab path.
- **Grant-revoke verification probe job.** Drafted, deferred seven times.
- **The floor — avatar render fallback.** **Check the wall.**
- **Founder tier-tag verification.** Flip ON → confirm 32 lock / 16 open → flip OFF.
- **Onboarding screenshots for screen 3.** Seed a demo hold by hand (~15–20 min), screenshot from the phone.
- **Measure the Smith machine offset.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**

---

## NEXT (soon — off the critical path)

### The rename's tail

- ✅ **`Quest`→`Bounty` IS LANDED.** The 07-31 coverage grep found `onboarding.first-quest.tsx` completely unswept (eight strings) plus two single misses, fixed all ten, and confirmed `public/`, the PWA manifest and every shipped `.json`/`.md` were already clean. **The rename is closed.** ⚠️ *Except the `verbLabel` enum leak, which is a rendering bug and not a string.*
- **`north-star.md` string inheritance** — ✅ **folded during the 07-31 regeneration.**
- **The Haiku vocabulary sweep has one target left:** `Feast`→`Hall`, and user-facing `points`→embers. **Check whether other `points` instances survived** — the `/first-run/adult/approving` one was fixed with the Bounty sweep.

### Onboarding, phase three

- **Walk the kid joiner flow with an actual kid.** Three beats to the adult's five, built and published, never exercised. **Blocked on the shared-device path until the marker read/write pair lands.**
- **Add the install-tutorial screen to the joiner flow.**
- **The `/first-run/*` copy deserves a second read now that it's live and renamed.** ⚠️ **Now part of the screen copy pass.**
- **The stacked-Pip-voice line on the first setup screen.**
- **A creator who bails mid-onboarding gets the joiner tour on return**, not their resumed setup. Degraded, not broken.
- **`member_admitted` renders as `"Mom · Leo"`.** One case in two switches.
- **The early-approval seam.** Approving a weekly before its due date produces a successor in the same week.

### Toolchain

- **⚠️ `routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** The generated and committed files disagree, every build surfaces it, and every agent has to know to throw it away.
- **`progression.test.ts` has a pre-existing `tsc --noEmit` error** — missing `vitest` types. **Surfaced on all four Code jobs today.** Clean it with the `progression.ts` job.
- **Claude Code's "sync before reading" rule needs a forcing function.** ✅ **Worked four times today** — every job reported the hash before reading. **Keep the line in every Code brief.**
- **`package-lock.json` is sitting untracked in the working tree.** The real lockfile is `bun.lock`. Noticed 07-31, unrelated to any job.
- **`wall_request_redemption` IS NOW CALLED FROM THE VAULT AND ITS NAME LIES.** Deliberate debt — renaming an RPC is a migration and the fix was free. **Rename when something else takes that function to Lovable.**

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

### Forge at stranger scale — what does NOT come along

Injury-prescription liability posture · commercial-gym equipment model. **Both are stranger-scale problems and Forge is household-scoped.**

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — monetization SKU #2.** Canvas particle-based, four registers: Forge · Hall · Garden · Keep. **Keep first as the performance proving-ground.**

**Wall-visibility ranks catalog items** — the wall is the only semi-public Emberhold surface a non-customer can encounter.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.** A retained household becomes worth $25 + $10 + $5 instead of $25 flat.

**Household-level unlocks only. Never per-kid, never per-class.** Free is a full tool; the purchase is delight, never access to basic function.

---

## KILLED / SUPERSEDED

- **A BRIGHT-HEAVY SLATE AS A PROBLEM — RESOLVED 2026-07-31, NOT PARKED.** Scott lived with `13 need doing` for a day and called it: it reads as honest. **The premise that a dim Slate is the healthy state was jAIne's, and it was wrong — the Slate's job is an accurate count, not a comfortable one.** If a Monday ever spikes it past comfortable, the lever is grouping, never re-hiding work.
- **TIGHTENING `wall_request_redemption` TO MATCH THE RLS POLICY — DECLINED 2026-07-31.** The 07-31 recon recommended it. It would have forced `_profile_id = auth.uid()`, meaning **only the household owner could ever redeem from the wall.** The recon treated the older RLS policy as correct and the newer RPC as deviant; it is the other way round — **the policy compares a profile id to an auth id, which only ever matches for the owner.**
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.** north-star asserted it; the wall has done all four by design for two weeks. **The doc was wrong, not the build.**
- **Marquee/scrolling titles — DECLINED.** Six simultaneously scrolling titles on a wall is a slot machine.
- **Title `maxLength` — DECLINED.** Killed by the photos: the truncating titles were 14 and 20 characters. **The container was the defect.**
- **The ADULTS ONLY badge as the truncation culprit — DISPROVEN.**
- **A shared row primitive threaded through ~18 call sites — SUPERSEDED.** Two independently-authored surfaces wrap correctly with no primitive. **The job is deleting a class at three sites.**
- **Archive-and-spawn on missed recurrence — DECLINED IN ADVANCE AND REPLACED IN CODE.**
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

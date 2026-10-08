# Parking Lot
**What might be.** Captured, not committed.

---

## How this works

Four buckets. **Inbox** is untriaged. **NOW** is the next work. **NEXT** is soon but off the critical path. **LATER** is backlog. **KILLED / SUPERSEDED** is the graveyard, kept so rejected ideas stay rejected.

**OPEN DECISIONS** is separate and it is not a waiting room. It holds questions genuinely unresolved and waiting on Scott. Anything decided moves to `decisions.md` and out of here.

**THE SCREEN COPY PASS** is its own running section below. It is a review inventory, not a backlog item.

⚠️ **THE PHASE BOUNDARY MOVED 2026-09-11.** Gate E (closed beta) runs ahead of Gate C (money and paperwork). **Beta-grade work — the email domain, a short true privacy policy, PostHog — is NOW.** Stripe, the Founding Guildhall build, refunds, tax and full COPPA posture stay in LATER until the beta has reported.

---

## Inbox (untriaged)

- **`ledger.tsx` and `slate.tsx` each carry their own `og:title`; the other 31 routes do not.** Needs a call on which is right.
- **An application-level export routine.** Real idea, wrong time. **Two manual exports now exist (09-30), so this is less urgent than it was.**
- **Should the reward audience enum get its own label map?** Two sites is not yet drift.
- **Streaks on standing duties.** `series_id` made them possible. ⚠️ **A streak is a guilt mechanic wearing a reward costume. The 10-08 guardrails apply: the reel and the recap are celebration only, and streaks do not sneak in under another label.**
- **Does the hearth panel want an upper bound?** The 09-30 fix handles up to eight members. A hold of twelve is not impossible.
- **Does the wall want last-done?** The wall is a different density problem and nobody has looked.
- **Does the wall have room for two reel tiles?** "Embers landed" and "Last week" now sit together. **Density on the ambient rail is Scott's eye.**
- **`beta_signups` rate limiting.** The honeypot stops form-filling bots, not a bot posting straight to the backend. **Only if spam actually shows up.**

---

## 🖊️ THE SCREEN COPY PASS — RUNNING

**Rule one (07-31): DELETE if the copy explains something the screen already shows. REWORD only if it teaches something invisible.**

**Rule two (08-01): NO EM DASHES IN USER-FACING COPY — and none in briefs handed downstream.** En dashes in ranges are fine.

**Rule three (08-03, amended 09-11): vertical height on a scrolling phone board is an argument, not a trump card. Measure before spending it.**

**Rule four (09-11): a vocabulary sweep must search synonyms, not just the retired word.**

**What GOOD looks like** — both kept deliberately: the Vault's *"Each request goes to a Keeper"* and the wall's *"A KEEPER APPROVES ON THEIR PHONE. NO EMBERS MOVE YET."* Both teach the invisible approval gate.

⚠️ **TOUCHED IS NOT REVIEWED.** **Batch before firing.**

| Screen | State | Notes |
|---|---|---|
| **The Slate** | ✅ **Reviewed 07-31, closed 08-02** | |
| **The Ledger** | ✅ **Reviewed 07-31** | |
| **Auth** | ✅ **Reviewed** | |
| **Campaigns** | ✅ **Reviewed** | |
| **Calendar** | ✅ **Reviewed** | |
| **Briefing** | ✅ **Reviewed** | |
| **`/welcome`** | ✅ **Copy live and checked 10-08** | *Placeholder now "The Ashford Hold." Safety section claims are true under own-session.* |
| **The wall** | ⬜ unreviewed | ⚠️ **Ranks first among the unreviewed. Now carries two reel tiles.** |
| **The reel and the weekly recap** | ⬜ unreviewed | *New 10-08. "While you were away," "Campaign complete," "kept the hearth lit," the chip and tile labels.* |
| **Ranks** | ⬜ unreviewed | *Weekly default, "All time" toggle, "Last week" label, new 10-08.* |
| **The Vault** | ⬜ unreviewed | ⚠️ **The Kin empty state still says "adult."** |
| **The Board** | ⬜ unreviewed | |
| **Quest detail** | ⬜ unreviewed | |
| **Lists** | ⬜ unreviewed | *"Add [search text]" on empty search, briefed 10-08.* |
| **Onboarding — add family** | ⬜ unreviewed | ⚠️ **PipSpark says "this adult."** |
| **Onboarding — recap** | ⬜ unreviewed | ⚠️ **"Adults turn dishes..." and "Adults approve."** |
| **Onboarding — first bounty** | ⬜ unreviewed | ⚠️ **High-stakes screen; read it whole.** |
| **PipHelp** | ⬜ unreviewed | ⚠️ **Kin topic says "rewards the adults set up."** |
| **Notifications** | ⬜ unreviewed | |
| `/quest-log`, `/hearth-log` | 🚫 **out of scope** | Debug surface, kept until the pre-production sweep. |

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **🟠 DOES THE ACTIVITY FEED'S RESOLVED NAME STAY FROZEN?** Frozen at write time or looked up live? **Blocks the backfill design.**
- **🟠 IS `kids-only` A DEAD AUDIENCE VALUE?** One grouped read settles it. **Do not kill it on 92%.**
- **🟠 DOES THE WALL WANT AN AUDIENCE BADGE?** Scott's eye.
- **🟠 SHOULD LISTS GET AN AUDIENCE FLAG?** jAIne's lean: leave it.
- **🟠 DOES A NULL `series_id` DESERVE ITS OWN EMPTY STATE?** Depends on the unreported backfill count.
- **🟠 DOES A MEMBER WHO EARNED NOTHING APPEAR IN THE WEEKLY RECAP?** Today they get no beat and are absent from the finale. **Celebration-only says omit. "Whole house" says show them, without a zero.** Mia has been at zero for four reported weeks. **Watch Monday's recap with the family before deciding.**
- **EMPTY ROSTER SEAT — auto-default an avatar, or a tappable "pick your hero" seat?** jAIne's lean: tappable seat; the wall is the exception. **Raised 07-29, still unratified.**
- **Should `campaign.$id`'s create gate be removed, or should the FAB gain one?**
- **Store shape — one-time founding unlock, a cosmetic catalog, or both? ON A CLOCK.** Founding Guildhall is LOCKED at $25. **Hard deadline: decided by July 2027 if Emberhold is still running.** *10-08 adds a candidate catalog item: trophy frames (see LATER).*
- **Module navigation.** Is seven tabs one too many on its own merits?
- **⚠️ Staging / dev database before the beta?** Local dev points at production. **The Gate E reorder makes this sooner.**
- **QA #5 — in-hold admin tier vs cross-hold super-admin.**
- **The founder paywall flip — timing only.** The grandfather write runs as `service_role`.
- **Quality — the two open halves.** Visible to Kin or Keeper-only, and what consumes it.
- **Ranks as a household dial.** ✅ **Half-answered 10-08: weekly Ranks is the softening.** A weekly reset means a kid who lost this week starts fresh Monday instead of falling further behind forever. **What remains: whether a hold should be able to hide Ranks entirely.** Probably not needed now.
- **Unify `quest.audience` and `reward.audience`?** Only if it earns its keep.
- **What replaces `/setup/intent`'s parking trigger?** Delete, repurpose, or re-park.

---

## NOW (this is the next work)

- **🔴 THE EMAIL DOMAIN SESSION.** One session, two payoffs. **(1)** A provider. **(2)** theemberhold.com verified with SPF, DKIM and DMARC at the registrar, Scott's step. **(3)** Auth email pointed at the new provider, **the step people forget.** **(4)** A ping to Scott when a row lands in `beta_signups`, skipping honeypot catches. **Verify across all six providers, especially the one that spams today.** *Inspect any NS-record request before pasting.*
- **🔴 A SHORT PRIVACY POLICY THAT IS TRUE.** Must name `flock.js`. Beta-grade.
- **🔴 POSTHOG.** Day 8 is Gate E's entire exit criterion.
- **WATCH THE WEEKLY RECAP, MONDAY 10-12.** On the wall, with the family. That is the verify. **Then settle the zero-earner question above.**
- **CONFIRM THE TWO UNREPORTED 10-08 PROMPTS RAN.** (a) Wall tile cools to a replayable resting state; hearth avatars back to true circles, with Lovable's report on what re-broke them; Lists "Add [search text]." (b) The two fire scales; chips on every profile's Board, Keepers included.
- **THE UNREQUESTED PACKAGE UPDATE REPORT.** Which packages, which versions, through bun? Then `bun audit`.
- **A KIN-PROFILE REEL CHECK.** Chip appears; parents-only beats never do.
- **THE WALL TABLET INSTALL.** Chrome proper, never Fully Kiosk.
- **BACKUP: A SECOND LOCATION.** Two exports exist on one desktop. A cloud copy.
- **THE FOUR "adult" STRINGS** plus the grep report that never came back.
- **VERIFY THE PHONE BOARD'S CAMPAIGNS** · **THE `series_id` BACKFILL COUNT** · **🔴 THE THREE RENAME COMMITS (08-03).**
- **🖊️ THE SCREEN COPY PASS.** The wall ranks first; the reel's copy is new.
- **`board.tsx:149`'s kicker** · **`Testing retired`** · **the wall's `logActivity` in `mutationFn`** · **prod test-object cleanup** (`testing approve` minted 10 real embers to Mia) · **signup glass checks #2 and #3** · **the grant-revoke probe** · **the `quests.audience` grouped read** · **onboarding screenshots for screen 3** · **the Briefing's double claim** · **STALE chip predicate.**
- **Two derivations of role** — permanent by design since 08-03.

---

## NEXT (soon — off the critical path)

### The reel, round two

- **THE TROPHY CARD.** ✅ LOCKED in principle 10-08: **phone only, one card per member per week, free.** Its home is the weekly recap's member beat: Mia's week, framed, saveable as an image. **Styled hotter as the haul grows.** Build after Monday's recap verifies. Saving on iOS is its own failure surface; test there first.
- **LIVING FIRE.** The fireplace is painted and static. Lovable was clear that convincing moving flames need looping video. **This is an asset task, Scott's lane:** four short seamless loops (coals, low flame, full fire, roaring), generated or licensed, matched to the painterly hearth. **The firebox is already structured to crossfade video per tier without rework.** Then the sconces, same treatment.
- **THE NUMBER'S STYLE.** It went from gold to cream with a heavy black outline. **Legible, but it reads slightly like a sticker.** A warm gold-white with a softer dark glow is worth one try. Scott's eye.

### The profile dashboard

**Direction decided 10-08 (Scott and May), design open.** The profile's "bounties open" page becomes a personal dashboard that drives personal competition: open bounties, this week versus last week, weekly average, bounties completed, best week.

- **jAIne's lean: you versus last-week-you is the competition.** Ranks already handles sibling comparison; the dashboard is the private-progress half.
- **Guardrail: a down week shows gently, never as failure.** One quiet line, the last-done precedent.
- **Guardrail: no streaks under a dashboard label.**
- **Mostly existing data:** the earned-by definition, the household week, `series_id`, and the reel's windowed earnings all exist.

### Beta readiness

- **RECRUIT THE FOUNDING HOUSEHOLDS.** Target 5 to 10. **In order: second-degree strangers, gamer-parent communities, then big parenting groups posted as a dad.** ⚠️ **Nothing through WCSD. Recruit outside Reno.** **Friends-of-friends can get the `/welcome` link now; cold strangers wait for the email domain.**
- **Skip beta-tester communities.** They say nothing about day 8.
- **The Founding Guildhall comp list.** Record beta hold IDs as they join. They do not count toward the 27.
- **Keep the one-shot venues sealed** until after Gate D.
- **A vacation is not churn.** The hold went to zero for two weeks during Brazil. **Ask beta households about travel before reading a week-two dip as a loss.**

### Toolchain

- **⚠️ BUN IS THE PACKAGE MANAGER.** **Plus, as of 10-08: "no dependency changes without asking" in every brief.**
- **🔵 `bun audit` baseline 2026-08-02.** Re-run now.
- **🔵 THE 47 TANSTACK TYPECHECK ERRORS** — deliberately not fixed.
- **⚠️ `routeTree.gen.ts` drift** · **`query_quest.mjs` untracked** · **Claude Code "sync before reading"** · **`wall_request_redemption`'s name lies** (deliberate debt).
- **⚠️ PLAN-MODE ITERATIONS ARE BILLED.** Approve-with-changes in one reply.

### The activity log rework

*Design settled 08-03. Blocked on credits and the frozen-name question.*

1. **`subject_profile_id`**, with `actor_id` meaning who clicked. Four of eight write sites conflate them. **The 10-08 earned-by definition is the natural source for `subject_profile_id`.**
2. **`logActivity` moves server-side.** The pattern is already shipped twice.
3. **The backfill.** Blocked on the frozen-name question.
4. **Canon claims three verb switches; recon found two.**

### Onboarding, phase three

- **The kid joiner flow** (downgraded 08-02) · **the install-tutorial screen in the joiner flow** · **`/first-run/*` copy second read** · **the stacked-Pip-voice line** · **a creator who bails mid-onboarding gets the joiner tour** · **the early-approval seam.**

### Everything else

- **Vault favorites → per-profile persistence** · **Quality, a rating with no consumer** · **Re-forge reach across the 13** · **Ghost successor cleanup** · Quick Add default EXPANDED on an empty board · the Lists "OPEN · DONE" fossil counter · Pip help discoverability · reward scarcity limits · yearly/monthly event recurrence · multi-day calendar events · calendar alerts · wall ticker speed · wall calendar pill member color and truncation ("Butch…") · "Forgot PIN" `confirm()` copy · `decisions.md` header missing SUPERSEDED.

---

## LATER (backlog)

**The offline shell / app-shell precache** · PWA push · Smart Lists v2 · Adventure Log · earning campaigns · admin/reporting surface · the strangers-grade wall (kiosk hardware + the P4×L8 pass on its write surface) · photo avatars · kid-vs-kid impersonation · favorites on the wall · the timezone nudge · the collaboration profile · an application-level export routine · `beta_signups` rate limiting.

**From the 10-08 reel work, deliberately later:**
- **Per-member register environments.** Each member's beat plays in their register's world: a smithy glow for Forge, lantern light through leaves for Garden, and so on. **Class-to-theme reuse, the cleanest kind of new.** Prove one stage first, which is done; build the others after the beta.
- **Persistent sconces.** One sconce per completed campaign that stays lit, so the hearth fills with light as a family's history grows. **Lovely, and it is persistent state that needs its own thinking.**

**⚠️ FLAT / PEER HOLDS IS THE SINGLE NAMED REOPEN TRIGGER FOR THE OWN-SESSION FORK.** It is not a feature request. It is an architecture change wearing one.

### GATE C — money and paperwork

**Stripe · the Founding Guildhall build (checkout, webhook, entitlement write) · refund posture · tax posture · full COPPA posture · the Pip-guided install tutorial.** At least fifteen items; a zero-credit decomposition session would size it. **Runs after the beta.**

**⭐ THE AVATAR FREE/PAID SPLIT** belongs with the SKU. Building it now ships locks on 32 faces with nothing to unlock them.

⚠️ **`system_flags` PRECONDITION.** Narrow the read policy before the first non-public or non-boolean flag lands.

### Pre-production sweep

**`/quest-log` and `/hearth-log` route deletion** · **prod test-object cleanup** · killing `kids-only` if the grouped read comes back empty.

### ⭐ SUSTAINING REVENUE (post-launch) — *named stream*

**Living-hold ambient theme packs — SKU #2.** Canvas particles, four registers. **Keep first.**

**Wall-visibility ranks catalog items.**

**Trophy frames (NEW — 10-08).** The base trophy card is free. **Fancier frames are a natural household-level catalog item.** Free is a full tool; the purchase is delight.

**The catalog is leverage on retention succeeding, not insurance against acquisition failing.** **Household-level unlocks only. Never per-kid, never per-class.**

---

## KILLED / SUPERSEDED

- **THE ARCHED-HALL REEL STAGE — SUPERSEDED 10-08, SAME SESSION.** It landed well and Scott loved it, then replaced it with the fireplace. **The arch was a backdrop with a number on it; the fire is the tier mapping itself.**
- **A CODE-DRAWN "ATMOSPHERIC" CASTLE — jAINE'S LEAN, WRONG, 10-08.** jAIne steered away from a detailed castle expecting clip art next to the painted avatars. Lovable went painterly and it sat right beside them. **Scott's push past "atmospheric" was right.**
- **MOVING THE AVATAR OUT OF THE FIRE — DECLINED 10-08, SCOTT'S CALL.** jAIne read the avatar covering the firebox as a composition defect. **Scott: the member stands in their own achievement.** Legibility was solved with backlighting and a scrim, not relocation.
- **THE FIRE ROARING FOR A CAMPAIGN — SUPERSEDED 10-08 BY THE SCONCES.** jAIne's first brief had the fire go to full intensity for a campaign beat. **Scott's sconce idea kept the fire honest: fire means embers only.**
- **AN EMBER TOTAL ON THE CAMPAIGN BEAT — KILLED 10-08.** Lovable displayed the sum of the campaign's bounties. **Those embers were already celebrated in their own beats; showing the sum counted them twice.**
- **THE WALL TILE DISAPPEARING ONCE WATCHED — SUPERSEDED 10-08.** jAIne's original brief. **Scott found the wall had no replay.** The tile now cools to a resting ember instead.
- **A PER-PROFILE REEL SHOWING ONLY YOUR OWN BEATS — SUPERSEDED 10-08, SCOTT'S CALL.** Everyone's beats in every reel, for FOMO and the whole-house feel. "New" stays per profile.
- **A DATABASE GUARD REFUSING CLAIMERLESS APPROVALS — DECLINED 10-08, SCOTT'S CALL.** jAIne wanted "the claimer, full stop" enforced by the database. **Scott kept the assignee fallback:** it never fires today and harms nothing if it ever does.
- **"RESTORE FROM DONE" IN LISTS SEARCH — DECLINED 10-08.** jAIne designed a second path to bring back checked-off items. **Search already shows done items and unchecking is one tap.** Only the "Add [search text]" button survived.
- **LIFETIME AS THE DEFAULT RANKS VIEW — SUPERSEDED 10-08.** May's call: weekly lands better. Lifetime stays as a toggle.
- **A FULL BOUNTY LIST FOR EVERY MEMBER ON ONE RECAP SCREEN — DECLINED 10-08.** Unreadable on a phone and from across a kitchen. **Totals on the final screen; tap a member for their list.**
- **THE FORGE, OPTION A — DECLINED 2026-09-11, SCOTT'S CALL.** Comparative advantage. Zero modules; every dollar of the $636 rides on strangers. **Option B dies with it.**
- **SWIPE CAROUSEL FOR WALL CAMPAIGNS — SUPERSEDED 2026-09-11.** Right pattern, wrong surface. **Replaced by the timed rolodex.**
- **CROSSFADE AS THE WALL'S CAMPAIGN TRANSITION — DECLINED 2026-09-11.** An ad rotator.
- **THREE-LINE WRAP ON LIST ROWS — DECLINED (09-11 tail).** Reads crowded.
- **THE VAULT AS ITS OWN LANDING-PAGE SECTION — DECLINED (09-11 tail).** It closes the loop's fourth beat.
- **TAP-AND-HOLD TO REVEAL TRUNCATED LIST TEXT — DECLINED 2026-09-11.** An invisible gesture for a wasted-space problem.
- **A REAL LIST IMPORTER — DECLINED 2026-09-11.** The clipboard is the API.
- **PASTE-TO-SPLIT IN BOUNTY CREATION — DECLINED 2026-09-11.**
- **CAMPAIGNS AND THE BOARD AS A CHECKLIST SURFACE — DECLINED 2026-09-11, BY EXPERIMENT.** The membrane working.
- **LAST-DONE KEPT OFF BOARD CARDS — REVERSED 2026-09-11, SCOTT'S CALL.**
- **PER-MEMBER AUTH — DECLINED 2026-08-03.** Reopens only on flat or peer holds.
- **"KIDS ONLY WAS ALWAYS A LIE" — jAINE'S ARGUMENT, WRONG, 2026-08-03.**
- **"AVAILABLE TO ANYONE" — DELETED 2026-08-03.**
- **"THE Parent/Keeper RENAME WOULD SEAM THE FEED HISTORY" — WRONG, 2026-08-03.**
- **AVATAR ROSTER TRANSPORT AS OUTSTANDING WORK — CORRECTED 2026-08-03.**
- **THE 47 TYPECHECK ERRORS AS AN npm ARTIFACT — DISPROVED 2026-08-02.**
- **"ADULT PIN STORED IN PLAINTEXT" — DISPROVED 2026-08-02.**
- **REVOKING `sandbox_exec`'s EXECUTE GRANTS — DECLINED 2026-08-02.**
- **`approve_quest()` AS A NEW RPC — DECLINED 2026-08-02.** A trigger instead.
- **BOUNTY DELETE ORPHANING ITS CALENDAR EVENT — NEVER REAL, 2026-08-01.** · **THE SLATE PANEL/HEADER MISMATCH — NEVER REAL.**
- **DELETING THE STANDING-DUTIES BLURB — REVERSED, 2026-08-01.** It is the empty state.
- **THE OFFLINE SHELL — DEPRIORITIZED 2026-08-01, NOT KILLED.**
- **FIXING THE `verbLabel` ENUM LEAK — DECLINED 2026-08-01.**
- **TIGHTENING `wall_request_redemption` TO MATCH RLS — DECLINED 2026-07-31.**
- **"THE WALL NEVER MINTS, SPENDS, APPROVES OR EDITS" — CORRECTED 2026-07-31.**
- **Marquee/scrolling titles — DECLINED.** · **Title `maxLength` — DECLINED.** The container was the defect.
- **A shared row primitive through ~18 call sites — SUPERSEDED.**
- **Archive-and-spawn on missed recurrence — DECLINED, REPLACED IN CODE.** Its lineage gap closed 09-11 by `series_id`.
- **Ember tier heat on the Slate — SUPERSEDED.** · **A Slate/Ledger segment control — SUPERSEDED.** · **A numeric Slate collapse threshold — DECLINED.**
- **The Ledger's record-vs-scrapbook fork — DISSOLVED.**
- **Capacitor — DECLINED**, two reopen triggers. · **Kid-auth — DECLINED.** · **Scripted screenshot capture — DECLINED.**
- **XP — killed 2026-07-10.** · **"Layer" — retired.** · **Cinder and Holt — DECLINED. The mascot is PIP.**
- **"Quest" as the universal object term — SUPERSEDED 2026-07-30.**
- **"Parent" and "Kid" as role labels — SUPERSEDED 2026-08-03.** KEEPER and KIN.

---

## 🟠 THE ROW PRIMITIVE — STILL OPEN, STILL SMALL

The job is deleting a declared `truncate` at three sites. ✅ **The List half closed in the 09-11 tail.** ⚠️ **The wall remains the open half:** a fixed-height ambient rail where wrapping may push rows out of view. Today's wall screenshot shows a calendar pill truncated to "Butch…". **Scott's eyeball, not a brief.**

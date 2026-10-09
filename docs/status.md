# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-10-08** — *the hearth session.* **Scott came back from Brazil itchy to build, and the app got its first payoff moment.** Embers used to land in silence: a kid finishes dishes at 4, a Keeper approves at 9, and nobody ever sees the reward arrive. **Now they land in a castle fireplace that burns hotter as the embers climb**, on the phone and on the wall, for Keepers and Kin alike.

**On the glass today:** the "While you were away" reel, built as a beat framework with a fireplace stage where the fire is the ember heat dial and the sconces are the channel for non-ember achievements. A campaign completion beat. A finale that credits the Keeper who kept the hearth lit. **One stop-clause fire caught a credit ambiguity in the data model before it shipped, and the fix made earnings a single shared definition.**

**Built, verification pending:** weekly Ranks and a weekly recap reel (unverifiable until Monday 10-12, because last week was empty). The `/welcome` beta form is tightened and safe to share with friends-of-friends.

**THE EVENING TAIL: the heroes came alive.** After the first close-out committed, the session kept going and shipped the thing the app was dreamed around. **Each family member's avatar now steps out of the fireplace as a generated video clip when they earn.** Four clips, about $2, made by hand on fal.ai with Kling v3 standard, stored in a shared clip library, and playing inside the firebox. **Glass-verified on the phone. The wall tablet is the last check.** Along the way: bounty categories drawn from 90 days of real data, three signature moves per category, and a five-step path from hand-made clips to clips generated automatically on approval.

**Also logged here: the tail of the 09-30 chat**, which ran past its own close-out again. The hearth panel fix ran and worked, backups exist, and the beta form audit found a real exposure.

Last session (prior): **2026-09-25** — the catch-up session. Android install verified on a real Pixel; service worker LOCKED.
Last session (prior): **2026-09-11** — the momentum session. Forge declined, Gate E ahead of Gate C, `series_id`, paste-to-split.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## 🧭 THE FRAME — GATE E BEFORE GATE C

**A free closed beta needs a short true privacy policy, an auth email that reaches the inbox, and PostHog so day 8 is visible.** Stripe, refunds, tax and the entitlement write come after the beta reports.

**Today was off the critical path on purpose.** Scott was itchy to build after five weeks away, and the reel was chosen because it aims directly at Gate E's question: *does a family open the app on day 8?* It gives a kid a reason to open the app that is not "a chore is waiting." **It is a retention feature built before strangers arrive, which the fence would normally forbid. The justification is that it is the only fence-adjacent idea that serves the beta instead of competing with it.** The critical path did not move today, and it is next.

---

## Where the platform is

**Structurally complete, published, installable on iOS and Android, with a working activation path, a working spend path for every role, the full 48-avatar roster live, and as of today a payoff moment.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, avatars, a household-local date model, verified tenant isolation, clean grant surfaces, the Slate, the Ledger, a proven rollover engine, a registered service worker, a server-validated approver, `series_id` lineage, **a single shared definition of who earned a bounty, a database-defined household week, and the reel.**

**Emberhold is a one-module product with zero modules.** Registers remain aesthetic only.

> **RESIDUAL:** master-spec has not absorbed today's design truth: the reel, the fire and sconce grammar, the credit rule, the week definition, weekly Ranks and the recap. **A dedicated spec fold session is needed.** Part II (Forge) remains documentation of a declined module.

---

## 🔴 THE CRITICAL PATH — PHASE 1, THEN THE BETA

| # | Item | Blocks |
|---|---|---|
| **1** | **⬜ THE EMAIL DOMAIN SESSION.** One session, two payoffs. Pick a provider, verify theemberhold.com with SPF, DKIM and DMARC at the registrar (Scott's step), **point auth email at the new provider (the step people forget)**, and add a signup ping to Scott when a row lands in `beta_signups`. **Verify by sending to an account at each of the six providers, especially the one that spams today.** A new domain has no reputation; expect a stretch of warm-up. | Gate B. Every stranger signup. Cold recruitment. |
| **2** | **⬜ A SHORT PRIVACY POLICY THAT IS TRUE.** Must name `flock.js`. Beta-grade. The `/welcome` safety section already makes true claims; the policy should match it. | The beta. |
| **3** | **⬜ POSTHOG.** Day 8 is unmeasurable without it. **The reel is now the first habit feature whose effect we would want to see in the numbers.** | Gate E's exit criterion. |
| **4** | **🟡 THE LANDING PAGE.** Plumbing safe as of today, copy live. **Shareable with friends-of-friends now. Not with cold strangers until #1 lands.** | Recruitment. |

**Off the critical path since 09-25:** ✅ backups (two exports exist) · ✅ the hearth panel five-member bug (fixed 09-30) · ✅ Android install.

---

## ✅ SHIPPED — 2026-10-08

### "While you were away" — the reel *(glass-verified on the phone)*

**One household reel with two entry points.** On the wall, an "Embers landed" tile that glows by heat and never autoplays. On the phone Board, a chip for the active profile. **Everyone sees everyone's beats**, Scott's call, for the FOMO and the whole-house feel. "New" is tracked per profile (Kin share devices) and per wall device.

- **The stage is a castle fireplace.** Painterly, ember-lit, with the avatar and the number standing *in* the fire, backlit by it. **Scott's call: the member stands in their own achievement.** A dark scrim keeps text legible at the hottest tiers.
- **THE FIRE IS THE EMBER HEAT DIAL.** It follows the bounty's tier: coals at the bottom, a roaring blaze at the top, rising and falling between beats. Coals at 10 and a blaze at 150 read instantly. **The fire means embers and nothing else.**
- **THE SCONCES ARE THE NON-EMBER CHANNEL.** A small idle flame on ember beats (Scott's default), and they ignite on a campaign completion while the fire holds steady. **A kid learns the grammar once: fire rose, someone earned; sconces lit, the hold won together.**
- **Campaign beat:** the campaign name is the hero, contributors beneath it, no ember count, plays after all ember beats and right before the finale. Sconces stay lit through the finale.
- **The finale:** total embers, each member in arrival order (never sorted by amount), and **"[Keeper] kept the hearth lit: N approvals,"** counting only approvals of *other* members' bounties. Self-approvals are excluded, verified on real data.
- **Pacing has hard minimums** after "too fast" came back twice: the fire settles over at least 1.5s, the count runs at least 1.5s, each beat holds at least 2s, and beats run roughly 6 to 8 seconds. Skip and tap-to-advance work throughout. Reduced motion is respected.
- **Caps:** 48 hours, 12 beats, so a cleared marker never dumps weeks of history.
- **Celebration only.** No denials, no misses, no rankings.

**What it took:** one framework prompt, then four art passes (dim modal → arched hall → fireplace → legibility), the sconce channel, and a campaign fix pass. **The art passes stopped when the remaining gap became an asset rather than a prompt.** Lovable said moving flames need looping video; painted fire is static. See the parking lot.

### Hero celebration clips *(migration + storage + glass-verified on the phone)*

**The family's avatars are alive.** SnowDad's golem cracks with lava, May smirks through a rush of heat, Cade's reaper raises his lantern, Mia's sprite bounces and twirls. Each is a 4-to-5-second generated clip.

- **How they're made (recipe v1):** fal.ai playground, Kling v3 **standard** (~$0.112/s, ~45 cents a clip), 4 seconds, the roster file as-is, **no end frame**, and a prompt that turns the avatar's baked-in ring into a **portal that burns away in the first second.** Hero description plus one celebration action plus style plus guardrails. ⚠️ **jAIne said the ring had to be cleaned out of the source first; Scott tested the prompt instead and it worked better.**
- **The library:** table `avatar_clips` keyed by **avatar id, kind and variant**, with a **status** (`approved` / `pending` / `rejected`), and a `hero-clips` bucket. **Clips belong to avatars, not people,** so any household that picks the golem gets the golem's clip.
- 🔒 **Writes are backend only.** The library is shared across every hold, so Lovable's first plan, "Keepers can upload," would have let a Keeper in any hold replace a clip every other hold sees. **Signed-in users read; insert, update and delete belong to `service_role` alone.** Policy report confirmed.
- **The fireplace is the screen.** The beat opens on the circle portrait; when the clip is ready it fades in over it inside the firebox opening, plays once from 0.3s and holds its last frame. Edges are feathered into the dark so no rectangle shows. Next member's clip preloads. No clip, a failed load, or reduced motion leaves the portrait exactly as before.
- **Not behind the fire, yet.** Each fire tier is one flat painted image, so nothing can sit behind its flames. **Living fire loops are what unlock that.**
- **The flare ring:** a gold pulse ring tied to the ember count-up flashed over the clips for a quarter second. The first fix relied on the browser noticing the clip was playing and failed on Scott's phone. **The second makes the portrait track the clip directly and switches its ring off for the whole beat.** Verified gone on the phone.

### The credit rule *(migration + verified)*

**The stop-clause fired: the database had no single "credited member" field.** A bounty records who claimed it and who it was assigned to. One bounty of 167 ("Weekly laundry," July 24) had both, Cade claimed and May assigned, and **ember totals counted those 10 embers for both of them.**

**Shipped: one shared definition of who earned a bounty, the claimer, with the assignee as fallback**, read by totals, Ranks, the reel and everything after. May's total went from 1,070 to 1,060; nobody's spendable balance changed. **The fallback never fires today:** every approve path records a claimer, so it exists only for a future screen or a hand edit. Scott kept it rather than adding a database guard.

- 🔵 **Keepers earn exactly as Kin do.** 137 of 167 approved bounties went to Keepers.

### `/welcome` beta form tightened *(Lovable-tested + glass-verified)*

- **Before today, any signed-in account could read, edit or delete the signup list.** All thirteen friends could have seen every stranger's email. **Now visitors and signed-in users can only insert**, tested as a visitor.
- **Duplicate emails** return the same success message as a first signup, so the form never reveals who is already on the list.
- **A honeypot** stops form-filling bots. It does not stop a bot posting straight to the backend; rate limiting is the next layer if spam appears.
- **The placeholder** is "The Ashford Hold," not the family surname.

### Two catch-up items from the 09-30 tail

- ✅ **THE HEARTH PANEL FIX RAN AND WORKED.** "Who's at the hearth" now adapts from one to eight members across one to four columns. Phone surfaces were audited: the Board scrolls, Ranks is a scrollable list, the Hub has no fixed-height assumption. ⚠️ **But see the avatar regression in PENDING.**
- ✅ **BACKUPS EXIST.** Two exports, stored on Scott's desktop. **Single location.** A cloud copy is the obvious second.

---

## 🟡 BUILT, VERIFICATION PENDING — WEEKLY RANKS AND THE WEEKLY RECAP

**The week runs Monday 00:00 to Sunday 23:59 in the household's timezone**, defined once in the database (`household_week_bounds`), never computed by screens. It runs with the caller's own permissions and returns only the caller's own household's week. **Ranks, the recap and the tiles all ask it.**

- **Weekly Ranks** is the default, with an "All time" toggle. On a Monday, until anyone earns, it shows last week's final standings labeled "Last week."
- **The weekly recap** is built on the reel framework: one beat per member who earned, in roster order, with their week's total, bounty count and biggest bounty; campaign beats with sconces; and a final screen where tapping a member opens their bounty list for the week.
- **Two fire scales:** member beats (under 20 coals, 20 to 59 low, 60 to 119 full, 120+ roaring) and the household finale (under 75, 75 to 149, 150 to 249, 250+). **Calibrated against real weekly totals**, after jAIne's guess that Keepers would roar every week turned out backwards.
- **Entry points:** a "Last week" tile on the wall and a "Last week's recap" chip on the phone, glowing until watched, replayable all week. Per-profile tracking via `weekly_recap_watched_at`.

**Why it cannot be verified yet:** the last two completed weeks were empty (Brazil), so the recap stays hidden until Monday 10-12, when this week, including today's approvals, becomes "last week."

---

## 🟠 NOTED — Mia shows zero embers in all four reported weeks

The recap celebrates only members who earned, so **Mia gets no beat and watches everyone else's.** That is the FOMO working as designed, and it is aimed at a young child. **Not a code problem: make sure she has bounties she can actually finish.**

## 🟠 NOTED — the app went dark during Brazil

**Two consecutive weeks of zero approvals across the whole hold.** A family on vacation stops using a chore app. **Worth knowing before the beta: a week-two dip in a beta household may be a trip, not a churn.**

## 🟠 NOTED — Lists have no audience flag

Lists are hold-wide with no audience pattern. Known, deliberate for now.

## 🟠 NOTED — the rename sweep searched the wrong word

The 07-31 sweep searched "Parent" and "Kid" and missed four "adult" strings: the add-family PipSpark, the onboarding recap (×2), PipHelp's Kin topic, and the Vault's Kin empty state. **The grep report never came back.**

---

## 🟡 PENDING VERIFY

- 🟡 **HERO CLIPS ON THE WALL TABLET.** Publish, then replay. Portrait fades into clip, plays once, holds; no stutter between members; no ring. **The first time a generated clip plays on kitchen hardware.**
- 🟡 **MIA'S CLIP SHOWS ITS EDGES.** Her backdrop is cold starry navy against a warm firebox, so a dark square reads behind her where the others melt in. **Judge it on the wall first.** Fixes: deeper feathering for every clip, or her next clip prompted with a warm, dark, firelit background.
- 🟡 **A CONVERTED TEST COPY OF SNOWDAD'S CLIP.** Lovable made one to preview in its test browser. **Confirm it was not left in the production bucket.**
- 🟡 **THE THREE-FIX WALL/LISTS PROMPT (10-08).** ✅ **Hearth panel avatars are true circles again, seen on the glass.** Still unconfirmed: the wall tile cooling to a replayable resting ember, and Lists "Add [search text]". **Lovable's report on what re-broke the avatars never came back.**
- 🟡 **THE THRESHOLDS + CHIPS PROMPT (10-08): WAS IT RUN?** The two fire scales above, and **chips on every profile's Board.** Lovable claimed the "Embers landed" chip only appears on the Kin board, which contradicts a Keeper Board screenshot from the same morning. **Keepers getting their moment was the reason parents were included.**
- 🟡 **THE WEEKLY RECAP — MONDAY 10-12.** Watch it on the wall with the family. Check member beats, the fire spread, the finale, and the tap-a-member list.
- 🟡 **THE REEL ON THE WALL.** The tile played and disappeared, which is how the replay gap was found. **Fire transitions on the tablet have not been judged.**
- 🟡 **A KIN PROFILE.** The chip appears for Mia or Cade, and a parents-only approval never shows in their reel.
- 🟡 **THE UNREQUESTED PACKAGE UPDATE.** Lovable applied a "routine security update" mid-session without being asked. **Which packages, which versions, and whether it went through bun and `bun.lock` were requested and never reported.** Re-run `bun audit` against the 08-02 baseline.
- 🟡 **THE TIMEZONE HEAL — DRAFT until proven from a non-Pacific device.** **Scott was in São Paulo for the whole trip.** Did anyone open Emberhold there, and was "today" right? If so, it is a free LOCKED.
- 🟡 **THE PHONE BOARD'S CAMPAIGNS** · **THE `series_id` BACKFILL COUNT** · **THE "adult" GREP REPORT** · **🔴 THE THREE RENAME COMMITS (08-03)** · **THE WALL TABLET INSTALL** from Chrome proper.
- 🟡 **THE MONTHLY ROLL BRANCH.** September 1 and October 1 passed unobserved; last-done dates are the passive evidence.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** One line.
- 🟡 `/create?recurring=true` direct-URL half · `Testing retired` stays retired · the ember progress trail · Phaeaz cold-account retest · min password length 6→8 · signup glass checks #2 and #3.
- 🅿️ **`/setup/intent`** — its parking trigger (Forge) can never fire. Needs a new disposition.

---

## ⬜ OPEN — the next work, in order

- ⬜ **THE EMAIL DOMAIN SESSION.** Critical path #1.
- ⬜ **THE PRIVACY POLICY.** Critical path #2.
- ⬜ **POSTHOG.** Critical path #3.
- ⬜ **THE TROPHY CARD.** LOCKED in principle. **Its home is the weekly recap's member beat:** one saveable card per member per week, phone only, free. Next fun build after the recap verifies.
- ⬜ **BOUNTY CATEGORIES.** A category field on the bounty, carried on roll-forward, with AI pre-selection at creation. **The step the clip library and every future automation depend on.** See the parking lot.
- ⬜ **The profile dashboard.** Direction decided, design open. See the parking lot.
- ⬜ **The "adult" strings and the grep report.**
- ⬜ **🖊️ THE SCREEN COPY PASS.** The reel's copy is new and unreviewed too.
- ⬜ **`logActivity` server-side.** Design settled 08-03.
- ⬜ **Vault favorites → per-profile persistence.**
- ⬜ **The grant-revoke verification probe.** Deferred twelve times now.
- ⬜ **Is `kids-only` a dead audience value?** One grouped read.
- ⬜ **Three test objects are user-visible.** `testing approve` minted 10 real embers to Mia.
- ⬜ The Briefing makes the same claim twice · `board.tsx:149`'s kicker · the wall has no audience badge.

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Fixed and verified today:**
- ✅ **The shared clip library cannot be written by any hold.** `avatar_clips` and the `hero-clips` bucket are read-only to signed-in users; writes are `service_role` only. **A shared, cross-hold table with a role-only write check is a tenant-isolation hole by another name.**
- ✅ **`beta_signups` grants.** Signed-in users could read, update and delete the signup list. Now insert-only for visitors and signed-in users. Tested as a visitor.
- ✅ **The week function** runs with the caller's own permissions and takes no household argument. A draft that took a household id under definer rights, with a comment promising a check the code did not contain, was corrected before applying.
- ✅ **Both "mark watched" functions** check that the profile belongs to the caller's household, not that the profile equals the signed-in user. Kin have no accounts.

**Settled by the own-session fork (08-03):** adult PIN lock not tied to permission checks · redemption on behalf of another member (`wall_request_redemption`, permanent design).
- ⬜ **Kin read `adults_only` reward names and costs** and **`parents_only` quest details** — mark ignored, action pending.

**Marked ignored, reasoning on record:** public SECURITY DEFINER execute (lint 0028/0029) · forged shared activity-log entries (`actor_label`) · system flags readable by any authenticated user.

**Accepted with a condition:** 🔵 **`public.system_flags`** — narrow the read policy before the first non-public or non-boolean flag lands. A Gate C precondition.

**Real, open:**
- ⬜ **🔴 THE SERVICE WORKER IS A SECURITY SURFACE.** It caches nothing. **Future caching must never cache a response carrying an Authorization header.**
- ⬜ **fal.ai is a new third party.** Today it only receives illustrated avatar images and prompts, by hand. **When generation is automated, the API key lives server-side only, the callback is verified, and fal is named in the privacy policy beside `flock.js`.** No names, no photos of anyone, ever.
- ⬜ **`beta_signups` has no rate limit.** The honeypot stops form bots, not direct posts. Fine at this scale.
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **`flock.js`** — must be named in the beta privacy policy.

**Earlier fixed and verified:** `quests.approved_by` validation · `mark_first_run_complete` scoping · redemption approve/deny attribution · public/anon SECURITY DEFINER execute · `anon` CRUD across all tables. **Disproved:** PIN plaintext in `localStorage` · "Forgot PIN" takeover · join-code → admin.

---

## 🧰 THE TOOLCHAIN

- **BUN IS THE PACKAGE MANAGER. NEVER npm OR yarn.** ⚠️ **Lovable changed dependencies unasked on 10-08.** "No dependency changes without asking" is now standard in every brief.
- 🔵 **`bun audit` baseline 2026-08-02:** ten findings, all dev-tree. **Re-run now; dependencies changed.**
- 🔵 **The 47 TanStack typecheck errors** are real, one class, deliberately not fixed.
- **`routeTree.gen.ts` drift is live.** **`query_quest.mjs` remains untracked.**

---

## 🔵 THE BUILD MODEL

- **RUN THE CHEAP EXPERIMENT BEFORE WINNING THE ARGUMENT. (NEW — 10-08.)** jAIne said a video model could not remove the baked-in ring and the source had to be cleaned by hand. Scott spent 45 cents testing a prompt instead, and the portal burn became the recipe. **When a test costs less than the debate, run the test.**
- **A SHARED TABLE NEEDS A HOUSEHOLD-AWARE WRITE RULE, OR NO CLIENT WRITES AT ALL. (NEW — 10-08.)** "Keepers can write" sounds scoped and is not: a role is not a hold. **For cross-hold data, writes go through the backend only.**
- **A FIX THAT DEPENDS ON THE BROWSER NOTICING IS A GUESS. (NEW — 10-08.)** The first ring fix waited on a playback signal Scott's phone never sent. The second made the component track the clip directly. **Fix the mechanism, not the instance, again.**
- **AUTO-ACCEPT RUNS ON BY DEFAULT, SO SAFETY LIVES IN THE BRIEF. (NEW — 10-08.)** Scott rarely turns it off, and canon kept writing a rule nobody follows. **Every stop-clause and "show the plan before applying" line does the work the toggle was supposed to do. Every prompt that touches the database says so in its first line.**
- **A STOP-CLAUSE IS WORTH MORE THAN A CORRECT INSTRUCTION. (Fifth fire, fifth time right, 10-08.)** "Stop if the credited member cannot be determined" found two people recorded on one bounty and turned a reel into a shared earnings definition.
- **BRIEF THE FRAMEWORK, NOT THE ANIMATION. (NEW — 10-08.)** v1 shipped a plain reel on purpose, and every art pass slotted in without touching the queue. The campaign beat proved the framework was generic.
- **"TOO FAST" NEEDS NUMBERS. (NEW — 10-08.)** Loose pacing language failed twice. Minimum durations in seconds fixed it in one pass. **Visuals stay loose; timing does not.**
- **A STATIC ASSET CANNOT BE PROMPTED INTO MOTION. (NEW — 10-08.)** Lovable said moving flames need video. **When the remaining gap is an asset, stop prompting. Assets are Scott's lane.**
- **ASK FOR THE DATA BEFORE SETTING A THRESHOLD. (NEW — 10-08.)** jAIne guessed Keepers would roar every week; the four-week report showed the opposite. A one-line report request beat the reasoning.
- **TESTED ON THE GLASS BEATS REASONED FROM A DOC. (Reinforced twice, 10-08.)** jAIne designed a "restore from done" path for Lists search; Scott's screenshot showed search already includes done items and unchecking is one tap. **jAIne moved the avatar out of the fire; Scott kept it in, and it reads better.**
- **THE CLOSE-OUT IS NOT THE END OF THE CHAT. (Reinforced 10-08.)** The 09-30 close-out was written mid-chat and the chat kept going. **Second time.** Any work after a close-out needs its own close-out. **And a close-out that is never committed is the same failure: twice in a row, the docs were written and not pushed.**
- **A LAYOUT THAT ASSUMES A HEADCOUNT IS A STRANGER BUG.** Test every whole-household surface at one member and at eight.
- **NAME THE SURFACE, NOT THE FEATURE.** · **A GESTURE IS A CLAIM ABOUT WHO IS TOUCHING THE SCREEN.** · **WHEN A FEATURE CANNOT BE BUILT, ASK WHAT IS MISSING FROM THE MODEL.** · **ROUTING AROUND A FEATURE IS A FINDING.**
- **ASK THE USER BEFORE BELIEVING THE DOC.** · **A RENAME IS AN INVENTORY PROBLEM**, synonyms included. · **A DECISION CAN DISSOLVE AN ITEM.** · **WHEN A VERIFY FLIPS, SWEEP EVERY PLACE THE OLD STATE IS ASSERTED.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.** Lovable said the reel chip was Kin-only; a screenshot from the same morning said otherwise.
- **A PLAN ITERATION COSTS A CREDIT.** Review once; approve-with-changes in a single reply.
- **NO EM DASHES IN USER-FACING COPY OR IN BRIEFS HANDED DOWNSTREAM.**
- **ANSWER THE QUESTION ACTUALLY ASKED.** · **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.** · **A STATUS DOC THAT SHOUTS TRAINS THE READER TO SKIM.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER.** Shipped behavior wins.
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.** · **BRIEF THE RECON TO DISPROVE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **DECOMPOSE BEFORE YOU PROMOTE.** The reel, the campaign beat and the weekly recap all decomposed to the same framework.
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** The defining bug class. Checked again today, in two new functions.
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.** Today's `beta_signups` finding was a grant, not a policy.
- **Fetch the canon before producing anything.** Docs live under `docs/`.
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis) · **Opus (tenant-isolation audit, and the jAIne seat).**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-10-08** — the hearth session. The "While you were away" reel on a fireplace stage, fire as the ember heat dial and sconces as the non-ember channel; campaign beat; the hearth line. One shared earned-by definition after a stop-clause found a double-counted bounty. Weekly Ranks and the weekly recap built on a database-defined Monday week. `/welcome` form tightened, closing signed-in read access to signups. **Evening: the four family heroes became generated video clips on fal.ai (Kling v3 standard) playing inside the fireplace, from a shared clip library writable only by the backend.** Bounty categories set from 90 days of data. Logged the 09-30 tail: hearth panel fixed, backups exist.
- **2026-09-25** — the catch-up session. Android install verified on a real Pixel; service worker LOCKED. Lists collapse, section counts, left-align and two-line wrap. `/welcome` built.
- **2026-09-11** — the momentum session. Forge declined; Gate E ahead of Gate C. Wall campaign rolodex. `series_id` lineage and last-done. Lists paste-to-split.
- **2026-08-03** — the decision session. Own-session fork LOCKED. Keeper and Kin shipped.
- **2026-08-02** — the assessment session. `quests.approved_by` validated server-side; bun installed.
- **2026-08-01** — service worker shipped; first-run marker fixed; August 1 roll-forward passed.
- **2026-07-31** — Vault kid-redemption fixed via the wall RPC.
- **2026-07-30** — the Slate and the Ledger; same-row roll-forward; `retired_at`.
- **2026-07-29 → 07-26** — master-spec fold; first-run; install tutorial; `families.timezone`; table grants closed; signup rebuilt.
- **2026-07-25 → 07-19** — the constitution restructure; the date seam; **P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.**
- **2026-07-16 → 06-26** — roster fixes, admit-on-approval, Claude Code as a build lane, avatar roster, XP killed, Vault rails, Lists v1, Campaigns, Calendar, PIN, Quest Log.

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-09-11** — *the momentum session.* **Four Lovable prompts, five features shipped and glass-verified, one stop-clause fire, one schema migration, one strategy reversal.**

**The headline: the ladder reordered and the app got three features it didn't have this morning.** Forge Option A is DECLINED — Scott's call, and the right one. That removes the only path to break-even that didn't require strangers, which is exactly why **Gate E now runs ahead of Gate C**: a free closed beta needs no Stripe, no refunds, no tax posture. Build the cash register after you know someone wants to shop.

**On the glass tonight:** the wall's campaigns rotate on a rolodex flip; recurring bounties finally have a lineage and show when they were last done and by whom; Lists accepts a pasted block and turns it into a sectioned list. All three verified by Scott, on his own device, on real household data.

**The first real-world test of Lists happened and it passed.** An 86-item, 10-section trip list pasted in one shot, sections parsed, no false triggers. That is also the switching cost for every beta household that currently keeps a list in Google Keep.

Last session (prior): **2026-08-03** — the decision session. Zero credits, four Code jobs, the own-session fork LOCKED.
Last session (prior): **2026-08-02** — the assessment session. ~1.9 credits.
Last session (prior): **2026-08-01 (late)** — the free session. Service worker shipped.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## 🧭 THE REFRAME — GATE E BEFORE GATE C

**The ladder used to build the payment rail, then the funnel, then ask whether anyone stays.** That order spends fifteen-plus Phase 2 items before learning whether day-8 retention exists at all.

**A free closed beta needs none of it.** Under own-session, Kin have no accounts, so COPPA exposure is thin. What a beta actually needs is small: a short privacy policy that is true, an auth email that reaches the inbox, and PostHog so day 8 is visible.

**What that promotes:** auth email deliverability stops being "deferred until the end," because the end just got closer. **What it demotes:** Stripe, refunds, tax, the entitlement write. Real, still next after the beta, not before it.

**Phase 1's definition is unchanged** and still Gate B's exit criterion: a stranger onboards unassisted, tenant isolation holds, the first email they receive is from Emberhold and reaches the inbox, offline shows a themed shell.

---

## Where the platform is

**Structurally complete, published, installable on iOS, with a working activation path, a working spend path for every role, and the full 48-avatar roster live.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, avatars, a household-local date model, verified tenant isolation, clean grant surfaces, the Slate, the Ledger, a rollover engine proven on a real month boundary, a registered service worker, a server-validated quest approver, a role vocabulary that does not assume a nuclear family — and as of tonight, **recurring duties that remember their own history.**

**Emberhold is a ONE-module product with ZERO modules.** Registers remain aesthetic only. Forge is declined; `enabled_modules` now has no consumer and no candidate.

> **RESIDUAL:** roughly 400 lines of master-spec Part II (Forge) and the schema detail remain unread since the 07-29/07-30 folds. **Part II is now documentation of a declined module.** A cold read is worth doing eventually; it is no longer urgent in any sense.

---

## 🔴 THE CRITICAL PATH — PHASE 1, THEN THE BETA

| # | Item | Blocks |
|---|---|---|
| **1** | **⬜ BACKUP POSTURE.** Self-service export at **Cloud → Advanced settings → Export data**. **Thirteen live accounts, no confirmed recoverable backup.** Raised at session open 09-11 and not confirmed done. **Zero backups to one backup is still the largest single risk reduction on this board and it still costs a click.** | Gate B. The beta. |
| **2** | **⬜ AUTH EMAIL DELIVERABILITY.** One of six providers still spams. **Was deferred pending DNS spend; the Gate E reorder makes it load-bearing now.** Every beta household's first impression runs through it. | Gate B. Every stranger signup. |
| **3** | **🟡 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper, not Fully Kiosk.** | Gate B. The wall device. |
| **4** | **⬜ THE LANDING PAGE.** Doubles as beta recruitment: *founding households wanted.* Pure visual, Scott's lane, Lovable's strength. **The first time Emberhold gets presented rather than debugged.** | Recruitment. |
| **5** | **⬜ A SHORT PRIVACY POLICY THAT IS TRUE.** Must name `flock.js`. **Beta-grade, not Gate C grade.** | The beta. |
| **6** | **⬜ POSTHOG.** Day 8 is unmeasurable without it, and day 8 is the entire question. | Gate E's exit criterion. |

---

## ✅ SHIPPED — 2026-09-11

### The wall's campaign rotation *(glass-verified)*

Campaigns on the wall were a single card with no way to reach the others. Now: **one card at a time, full width, rotating every 10 seconds on a rolodex flip hinged at the top edge, with dot indicators whose active dot fills as a countdown.** No swipe, no tap target, `prefers-reduced-motion` falls back to a fade but never stops rotating.

- **jAIne shipped a swipe carousel to the wall first and it was wrong.** Scott had said "on the board"; the prompt said Board; Lovable built it on the wall. The gesture was the right pattern on the wrong surface — a walk-past kiosk nobody swipes, on a screen where canon had already declined marquee text for exactly that reason. **The surface mismatch was jAIne's, not Lovable's.**
- **Crossfade was rejected in favor of the flip.** A crossfade reads as an ad rotator; a card turning reads as a mechanical object bolted to a kitchen wall.
- **The cost, accepted:** walk past at the wrong moment and your campaign is not on screen. Fine at two or three campaigns. **Revisit the cap if a hold ever runs eight.**

### `series_id` — recurring bounties now have a lineage *(migration + glass-verified)*

**The stop-clause found it.** Asked to show a last-completed date, Lovable reported back: roll-forward archives the old row and creates a brand new one with only title, points and cadence copied. **Nothing pointed back.** Title-matching was the only link available, and it breaks on rename or a duplicate name.

**That was never a missing date field. It was a missing identity.** Every approval was minting a stranger that happened to share a title — no streaks possible ever, no per-duty history, silent orphaning on rename, and the pile growing daily.

**Shipped:** a `series_id` uuid minted when a standing duty is first created and copied forward on every roll, so live and archived instances share one lineage. **A one-time backfill grouped rows by exact title plus cadence within each hold, linking only unambiguous groups and leaving the rest null.**

- **The backfill fails toward blank, never toward a fabricated date.** A duty renamed in the past does not match, stays unlinked, and reads "Not done yet."
- 🟡 **The linked-versus-null count was requested in the brief and never reported back.** If null is more than a couple, spot-check them — a null reads identically to a genuinely-never-done duty on the glass, and that is the only lie this feature can tell.
- **Streaks are now possible without a second migration.** Not built, not scoped.

### Last completed, on standing duties *(glass-verified)*

**"Last done Aug 28 · Mia"** on the Slate's Standing rows, on recurring bounty detail, **and on Board cards.**

- ✅ **Scott added it to Board cards and overruled jAIne's call to keep it off them.** The objection was vertical height on a scrolling board; the line costs about twelve pixels and the rows had room. **Tested on the glass beat reasoning from a doc.**
- **The point of the feature, seen immediately on real data: "Brush Chaos · last done Jul 30" on a monthly.** August never happened, and before tonight no surface in the app could have said so. **Roll-forward killed the guilt pile and quietly erased the evidence of a miss along with it. This puts the miss back as one quiet line.**
- **Long titles with large ember values wrap the date onto its own line** (`Bread winner`, +150). **Scott accepted it as-is:** the Slate is a when-needed surface and it is rare on Board cards.

### Lists: paste to split *(glass-verified on an 86-item paste)*

**Paste a multi-line block into a list's add bar and it becomes items.** Lines ending in a colon or starting with `#` become sections, reusing any that already exist. Bullets, dashes, numbering and checkbox marks are stripped. A confirmation states the count before anything is written, and the insert happens in one batch.

- ✅ **Verified on a real 86-item, 10-section trip list.** Section parse correct, count correct.
- ✅ **A mid-line colon did not false-trigger a section** — the rule is *ends with* a colon, and "Travel clinic: yellow fever and routine vaccines" stayed an item. **That was the flagged risk and it held.**
- **This is the import story, and there is no importer.** The clipboard is the API, so any AI, notes app or email becomes a list source for free. **It is also the switching cost for a beta household bringing a grocery list over from Keep.**
- **Deliberately NOT added to bounty creation.** Bulk-minting bounties is what cluttered the board in the first place.

### Two small fixes

- ✅ **The Briefing's content now clears the floating add button.**
- ✅ **Keepers get their own Vault empty state** inviting them to stock the first reward, with a button to the add form. Kin keep the ask-a-Keeper wording.

---

## 🔵 THE MEMBRANE HELD, AND THE TEST WAS ACCIDENTAL

**Scott tried to build a PAX interest-and-to-do list as bounties on the board and it cluttered the board.** That is not a campaigns defect. **It is the membrane working as designed:** the game lives in bounties; a checklist is a list.

**He had not tried Lists.** Lists already had user-defined sections and open/done counts and would have done the job on the first attempt. **Nothing needed building — the routing around a feature was the finding.** Brazil is the retry, and it worked.

---

## 🟠 NOTED — Lists have no audience flag

**Lists are hold-wide. There is no audience pattern on them at all**, which is a real exception to a load-bearing platform pattern.

Consequence, live tonight: a trip list containing gift items is readable by every member of the hold, including a Kin who is not on the trip. **Not a defect and not scheduled — but the kind of thing that gets rediscovered painfully in six months, so it is written down now.**

---

## 🟠 NOTED — the rename sweep searched the wrong word

**The 07-31 coverage grep searched for "Parent" and "Kid" and passed clean. Nobody searched for "adult."** Four user-facing strings survived it:

- `onboarding.add-family.tsx` PipSpark: *"only this adult can approve bounties"*
- Onboarding Recap: *"Adults turn dishes..."* and *"Adults approve."*
- PipHelp, Kin-facing topic: *"rewards the adults set up"*
- Vault, Kin empty state: *"Ask an adult to stock the vault"*

⚠️ **The Vault one shipped past tonight's own fix.** Item 4 gave Keepers a correct new empty state and preserved a Kin string that was itself unmigrated.

**Lovable initially reported the PipSpark string already correct.** There are four near-identical "only a Keeper can approve" explainers and three of them say "you" — it read a sibling and declared the family clean. **The fix was to hand it the literal string.**

🟡 **These rode in the rolodex bundle. The grep report never came back.** Cheap to close.

---

## 🟡 PENDING VERIFY

- 🟡 **THE PHONE BOARD'S CAMPAIGNS.** The original complaint was the Board; the fix landed on the wall. **The Briefing lists all campaigns, so the Board may already be fine.** One look, no credits.
- 🟡 **THE `series_id` BACKFILL COUNT.** Linked groups versus null. Requested, not reported.
- 🟡 **THE "adult" GREP REPORT.** Four known strings plus whatever else the sweep found.
- 🟡 **🔴 THE THREE RENAME COMMITS (08-03).** The Vault as Keeper and as Kin, the Board, quest detail, and **the wall**. Still unverified.
- 🟡 **🔴 THE ANDROID INSTALL PROMPT.** Scott's.
- 🟡 **THE MONTHLY ROLL BRANCH.** August 1 passed both legs. **September 1 has now passed unobserved; the Slate's last-done dates are the first passive evidence it is working.**
- 🟡 **THE TIMEZONE HEAL — DRAFT until proven from a non-Pacific device.** Scott's São Paulo trip was the candidate and has slipped to roughly October 1.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** One line.
- 🟡 **`/create?recurring=true`** direct-URL half · **`Testing retired` stays retired** · **the ember progress trail** · **Phaeaz cold-account retest** · **min password length 6→8** · **signup glass checks #2 and #3.**
- 🅿️ **`/setup/intent` — PARKED. Its trigger was "finalized when Forge is built." Forge is declined, so the trigger will never fire.** Needs a new disposition.

---

## ⬜ OPEN — the next work, in order

- ⬜ **THE LANDING PAGE.** Critical path #4. **Write the pitch first — what it is, who it's for, what you're asking of them.** Words are jAIne's lane, Scott holds the veto, Lovable builds from a loose brief.
- ⬜ **LIST ROW TRUNCATION.** Rows truncate mid-line inside a container with room for three. **Wrap to two lines, truncate past that, tap-to-expand for the rare leftover.** The same disease truncates the section chip in the add bar to "NO SEC…". **Tap-and-hold was declined: an invisible gesture solving a problem caused by wasted space.**
- ⬜ **BACKUP EXPORT.** Still one click.
- ⬜ **The "adult" strings and the grep report.**
- ⬜ **🖊️ THE SCREEN COPY PASS.** Slate ✅ · Ledger ✅ · Auth ✅ · Campaigns ✅ · Calendar ✅ · Briefing ✅ · everything else unreviewed. **Touched is not reviewed.**
- ⬜ **`logActivity` SERVER-SIDE.** Design settled 08-03. Blocked on credits and the backfill design, nothing else.
- ⬜ **Vault favorites → real per-profile persistence.**
- ⬜ **The grant-revoke verification probe.** Drafted, deferred eleven times.
- ⬜ **Is `kids-only` a dead audience value?** One grouped read settles it. Rides along on the next Code job.
- ⬜ **THREE TEST OBJECTS ARE USER-VISIBLE.** `Testing redemption tracking`, `Testing retired`, `testing approve`. **`testing approve` minted 10 real embers to Mia; deleting the bounty does not unwind the balance.**
- ⬜ **The Briefing makes the same claim twice.** Cosmetic.
- ⬜ **`board.tsx:149`'s kicker may stutter** against the `<h1>` below it. Scott's eye.
- ⬜ **The wall has no audience badge at all.** Cross-surface asymmetry. Scott's eye.

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Settled by the own-session fork (08-03), unconditional:**
- ✅ Adult PIN lock not tied to real permission checks.
- ✅ Redemption submitted on behalf of another member. **`wall_request_redemption` never compares `_profile_id` against `auth.uid()`. Permanent design.**
- ⬜ **Kin read `adults_only` reward names and costs** — should be marked ignored, action pending.
- ⬜ **Kin read `parents_only` quest details** — same. **Both were left visible pending the fork. The fork is decided.**

**Marked ignored, reasoning on record:** public SECURITY DEFINER execute (lint 0028/0029) · forged shared activity-log entries (`actor_label`) · system flags readable by any authenticated user.

**Accepted with a condition:**
- 🔵 **`public.system_flags`.** One global row. **Before the first non-public or non-boolean flag lands there, the read policy must be narrowed.** A Stripe mode flag or a kill switch would be world-readable to every account, silently. **A Gate C precondition — now further away than it was, since Gate E moved ahead of it.**

**Real, open:**
- ⬜ **🔴 THE SERVICE WORKER IS A SECURITY SURFACE.** It caches nothing, which is what makes it safe. **Any future caching work must never cache a response carrying an Authorization header.** A caching bug is the one defect here that cannot be fixed by pushing a fix.
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the beta privacy policy.**
- ⬜ ~~Forge display mode is a semi-public surface.~~ **Dead with the module.**

**Fixed and verified:** `quests.approved_by` adult validation (08-02, walked on the glass) · `mark_first_run_complete` profile-scoping · `approve_redemption` / `deny_redemption` attribution · public/anon SECURITY DEFINER execute · `anon` CRUD across all fifteen tables.

**Disproved:** adult PIN plaintext in `localStorage` · "Forgot PIN" takeover · join-code → Parent admin. **All three were scanner claims, all three false.**

---

## 🧰 THE TOOLCHAIN

- **BUN IS THE PACKAGE MANAGER. NEVER npm OR yarn.** Name it in every Code brief, like `routeTree.gen.ts`. **Held across seventeen Code jobs.**
- **Every prior `npm audit` result in this project's history is void.**
- 🔵 **`bun audit` baseline 2026-08-02:** ten findings, all dev-tree, none shipping. Re-run after any dependency change.
- 🔵 **The 47 TanStack typecheck errors are real, one class, and deliberately not fixed.** Type layer only, zero runtime impact. **Naming the number in the brief turns a polluted output into a control.**
- **`routeTree.gen.ts` drift is confirmed live.** Name it in every brief.
- **`query_quest.mjs` remains untracked.** Named in every brief and correctly left alone.

---

## 🔵 THE BUILD MODEL

- **TESTED ON THE GLASS BEATS REASONED FROM A DOC. (Reinforced 09-11.)** jAIne kept last-done off Board cards citing vertical height, from the spec. Scott shipped it and it costs twelve pixels. **Same failure shape as avatar transport on 08-03: canon beat the person who opens the app daily.**
- **A STOP-CLAUSE IS WORTH MORE THAN A CORRECT INSTRUCTION. (Fourth fire, fourth time right, 09-11.)** "If the only link is a title match, stop and report" turned a cosmetic date field into a schema fix. **Four fires, four correct, across three sessions.**
- **NAME THE SURFACE, NOT THE FEATURE. (NEW — 09-11.)** "Campaigns on the Board" produced a carousel on the wall, because the Board and the wall both render campaigns and only one of them has thumbs. **A brief that names the interaction without naming the device is half a brief.**
- **A GESTURE IS A CLAIM ABOUT WHO IS TOUCHING THE SCREEN. (NEW — 09-11.)** Swipe and tap are phone idioms. The wall is walk-past — canon had already declined marquee text there for the same reason, and jAIne re-derived the mistake anyway.
- **WHEN A FEATURE CANNOT BE BUILT, ASK WHAT IS MISSING FROM THE MODEL. (NEW — 09-11.)** Last-done looked like a date field. It was an identity field. **The blocked feature was the symptom; roll-forward's missing lineage was the disease.**
- **ROUTING AROUND A FEATURE IS A FINDING. (NEW — 09-11.)** Scott built a to-do list as bounties and cluttered the board, because he had not tried Lists. **The membrane worked; the discoverability did not.**
- **ASK THE USER BEFORE BELIEVING THE DOC.** · **A RENAME IS AN INVENTORY PROBLEM, NOT A STRING PROBLEM** — and the inventory must include synonyms, which is how four "adult" strings survived a "Parent"/"Kid" sweep.
- **A DECISION CAN DISSOLVE AN ITEM INSTEAD OF SCOPING IT.**
- **WHEN A VERIFY FLIPS, SWEEP EVERY PLACE THE OLD STATE IS ASSERTED.**
- **THE BOARD CANNOT DISTINGUISH "NEVER DONE" FROM "DONE DAILY."** ⚠️ **`series_id` makes this literally true of the app itself now:** a null lineage and a genuinely-never-done duty both read "Not done yet."
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.** `git show --stat` in every brief.
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **A PLAN ITERATION COSTS A CREDIT.** Review a plan in ONE pass.
- **NO EM DASHES IN USER-FACING COPY OR IN BRIEFS HANDED DOWNSTREAM.** The second is how the first happens.
- **ANSWER THE QUESTION ACTUALLY ASKED, AT THE REGISTER IT WAS ASKED.** · **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.**
- **A STATUS DOC THAT SHOUTS TRAINS THE READER TO SKIM. (NEW — 09-11.)** This board had accumulated more warnings than features and Scott stopped opening it. **Severity markers are a signal only while they are scarce.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH.** The one describing SHIPPED BEHAVIOR wins.
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.** · **BRIEF THE RECON TO DISPROVE.**
- **FIX THE CONTAINER, NOT THE CONTENT.** · **FIX THE MECHANISM, NOT THE INSTANCE.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** The defining bug class of the codebase.
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **Fetch the canon before producing anything.** Verify the file, not the tracking item that describes it.
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis, synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** · **Session lane, declared at open.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-09-11** — the momentum session. Forge Option A declined; Gate E promoted ahead of Gate C; beta recruitment channels and the WCSD exclusion set. The wall's campaign rotation rebuilt as a rolodex flip after a swipe carousel landed on the wrong surface. `series_id` lineage shipped with a one-time backfill, unlocking last-done on the Slate, bounty detail and Board cards. Lists gained paste-to-split and passed an 86-item real-world test. Vault Keeper empty state and the Briefing FAB overlap fixed.
- **2026-08-03** — the decision session. The own-session fork LOCKED, closing four items by reclassification. Keeper and Kin shipped across three commits. The activity label resolved to a two-column split. Zero credits. `9c83fc4` → `e8223f4` → `f9f731e`.
- **2026-08-02** — the assessment session. `quests.approved_by` validated server-side and verified on the glass; bun installed; the security panel triaged to zero mysteries; Stripe reframed as Phase 2. ~1.9 credits.
- **2026-08-01 (late)** — the free session. Service worker shipped for installability.
- **2026-08-01 (early)** — the marker session. First-run marker fixed. **August 1 roll-forward passed both legs.**
- **2026-07-31** — the redemption-path session. Vault kid-redemption fixed by routing to the wall RPC.
- **2026-07-30** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. **The decision that quietly created the lineage gap closed tonight.**
- **2026-07-29 → 07-26** — master-spec fold; first-run completion marker; install tutorial; the non-creator first run; `families.timezone`; table grants closed; five-screen Pip-guided first run; signup rebuilt.
- **2026-07-25 → 07-19** — the constitution restructure; the household-local date seam; the SECURITY DEFINER grant surface; **P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.**
- **2026-07-16 → 06-26** — roster fixes, admit-on-approval, Claude Code proven as a build lane, avatar roster, XP killed, Vault rails, Lists v1, Campaigns, Calendar, PIN, Quest Log.

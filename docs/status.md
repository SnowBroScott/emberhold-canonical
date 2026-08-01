# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-31 (night) → 2026-08-01 (early)** — *the marker session.* **~2.8 Lovable credits, two free Code jobs, two glass verifications.** **Critical path #1 is closed: the first-run completion marker now reads and writes the same profile for every role, verified on the glass by an actual profile switch to a kid.**

**The headline: a kid sub-profile can complete first run and stay completed. Both roll-forward legs passed on August 1. The `verbLabel` enum leak is scoped, contained and closed without a fix. And the em-dash sweep removed 125 characters of jAIne's punctuation from the glass.**

**The marker fix was wider than described and narrower to fix than expected.** `markFirstRunComplete(profileId)` had always been *handed* the right id and threw it away with a literal `void profileId`. Five call sites were correct; a shim discarded the argument. **And a second reader was owner-keyed too** — `FirstRunGate` read the route-context profile fetched `WHERE id = data.user.id`, so the divergence was write-owner / read-owner / read-active, not the two-way split canon described. **Fixed with one shared `resolveFirstRunProfileId()` that both readers call**, rather than the same expression written twice in two files.

**AUGUST 1 PASSED, BOTH LEGS.** Five monthly rows, no duplicates, every one rolled to Aug 1 in place. The throwaway daily stayed at July 31, still awaiting approval, ten hours after submission. **Same-row roll is real and the `submitted` exclusion works.**

**The `last done` line on the Slate row is doing more work than it was designed to do.** Vaccuum Downstairs read *May* on Aug 1 where the snapshot said *Cade* — alarming until `last done Jul 31` explained it: the chore got done and approved overnight, the row completed and rolled clean. **A row that moves instead of respawning has no history unless it carries one.** That field is what makes same-row roll auditable from the glass without a query.

**Credits are the lesson of the session.** Two plan-mode iterations cost a credit each. **A plan iteration is billed like a build.** The constraint that drove both of them — that the gate's null fallback must match the writer's — belonged in the original brief as an explicit line, not as prose intent.

Last session (prior): **2026-07-31 (day)** — the redemption-path session. Four free Code jobs.
Last session (prior): **2026-07-30 (night)** — the Slate + Ledger build. Five credits.
Last session (prior): **2026-07-30 (late)** — the QA/design session.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for every role — a working SPEND path for every role — and, as of today, a first-run marker that survives a profile switch.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, tenant isolation verified under live attack, clean function and table grant surfaces, the Slate, the Ledger, and a rollover engine **verified on a real month boundary**.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> ⚠️ **`master-spec.md` OWES A SMALL, NAMED FOLD — NOT A REGENERATION.** It sits at **773 lines** and was corrected 07-31; the stale-spec flag stays closed. **Four regions went stale overnight and jAIne named them from a grep rather than a read, deliberately:** line 8 (the `verbLabel` open question — now answered), lines 298–299 (the marker's ⚠️ read/write warning — now shipped), line 482 (`/quest-log` and `/hearth-log` as "deletion is a follow-up" — now reclassified), line 753 (the copy-discipline section — needs the em-dash rule). **A fifteen-minute surgical fold. Open the next session with it.**

> ⚠️ **RESIDUAL, UNCHANGED:** roughly 400 lines of master-spec — Part II (Forge), the onboarding sections, the schema detail — were not re-read on 07-31 and were not read today. Believed current from the 07-29/07-30 folds. **A full cold read is worth doing eventually; it is not urgent.**

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. | Gate B honesty. |
| **2** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. Gate C. |
| **3** | **Walk the kid joiner flow with an actual kid.** ✅ **Unblocked today** — the marker fix was the dependency. Built, published, three beats, never exercised end to end. | Gate B. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Cleared off the critical path 2026-08-01: the marker read/write pair.** See below.

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

---

## ✅ SHIPPED — 2026-07-31 (night) → 2026-08-01

*One Lovable job (~2.8 credits, two of them spent on plan iterations), two free Code jobs.*

### Part A — the first-run marker read/write pair *(Lovable, `eb93e73`)*

- ✅ **Dropped the zero-argument `mark_first_run_complete()`.** Not defaulted — dropped, so no old signature survives as a callable overload.
- ✅ **New `mark_first_run_complete(_profile_id uuid)`,** SECURITY DEFINER, `search_path = public`. Validates signed-in + `current_family_id()` match + `status = 'active'`, mirroring `wall_request_redemption`. **No comparison against `auth.uid()` — that comparison was the bug.** Grants applied `authenticated` first, `REVOKE FROM PUBLIC` second.
- ✅ **`resolveFirstRunProfileId()` added to `src/lib/first-run.ts`** — `getActiveMemberIdSync() ?? auth user id`. **Both readers call the one resolver.** `redirectIfFirstRunDone()` and `FirstRunGate` cannot drift, because there is only one expression.
- ✅ **`markFirstRunComplete(profileId)` now actually passes its argument.** It contained a literal `void profileId;` and called the RPC with no args. **Five call sites had been correct all along and a shim ate the id.**
- ✅ **VERIFIED ON THE GLASS: dad session → switch to Mia → first run fired (three Pip screens) → board, KID tag, 0 embers, live bounty. Switched away, switched back to Mia → straight to the board.** The second entry is the test; the first always looks fine.
- ⚠️ **`FirstRunGate` now re-resolves on member switch and reads the resolved profile's role.** Correct, and new behavior. **No misfire observed.**

### Part B — the `verbLabel` recon *(Code, read-only, free)*

- ✅ **All three surviving surfaces are clean and were verified in source, not inferred.** `wall.tsx`'s `tickerLine`, `Briefing.tsx`'s `pulseLine`, `NotificationBell.tsx`'s `lineFor` are **word-for-word the same switch** — five named verbs plus a `default` that returns `${who} · ${obj}` and never touches `row.verb`.
- ✅ **`hearth-log.tsx`'s `verbLabel()` is the sole place in the codebase that derives display text from the raw enum.** Deleting it would kill `QUEST APPROVED` outright.
- ✅ **`member_admitted` IS EXPLAINED.** The `activity_verb` enum has **seven** values; every renderer names five. `member_admitted` and `member_denied` are written server-side and fall to the label-only default, producing `"Mom · Leo"`. **Not a bug — two missing cases in three identical switches. A cheaper fix than the polish list assumed.**
- ✅ **The "four divergent implementations" item is DOWNGRADED.** Three are byte-identical; the fourth is a legacy variant on a page we are keeping deliberately. **That is duplication, not divergence. It is not the `isActiveQuest` failure mode and should not carry that severity.**

### Part C — the screen copy pass, batch two *(Code, free, `fd1d6a8`)*

- ✅ **Auth subhead deleted.** *"Your whole home, finally in one place."* — generic, and the WHAT IS EMBERHOLD block below said it better. Element removed, not blanked.
- ✅ **Campaigns description deleted.** *"Group bounties under a shared goal. Watch the bar fill as prep gets done."* Both sentences. The tab is labeled, the bounties are grouped on screen, and the bar is right there filling.
- ✅ **Calendar day empty state → "The day is clear."** Date-agnostic by design; the date header directly above it already names the day.
- ✅ **Briefing horizon empty state → "Clear skies ahead."** Extends the ON THE HORIZON eyebrow instead of decorating it. The dropped half explained the first half.
- ✅ **THE EM-DASH SWEEP: 125 characters removed across 43 files.** Page titles, `Skip —` labels, PIN ranges, and ~40 individual rewrites. **Verified: 125 removed, 0 introduced.**
- ✅ **Seven lines flagged rather than guessed** and left untouched — time/date ranges (en dash is correct typographic convention, not the AI tell) and bare `—` placeholder glyphs for "nothing set." **Correct restraint; do not touch them.**
- ⚠️ **`wall.tsx` held its own independent copy of the Briefing's empty state.** Code found it, fixed it, and disclosed it as beyond the four named edits. **This is the divergence pattern showing up in COPY, and it is the first instance that is not code.**

### Verifications closed — free, glass

- ✅ **🔴 AUGUST 1 — THE MONTHLY BOARD. PASSED.** Five rows under Monthly, all `MONTHLY · 1ST`, all "Due now," **zero duplicates.** No archive-and-spawn. The branch fired.
- ✅ **🔴 `submitted` DOES NOT ROLL. PASSED.** "Do Not Approve Testing Roll-Over," daily, created and claimed 10h prior, **still awaiting approval, never moved off its anchor.** The exclusion works on a row past its date, which is the only case that exercises it.
- ✅ **The claim discriminator was consumed by real life and the board explained itself anyway.** Cade's two claims changed because the chores were actually done and approved overnight. **`last done Jul 31` is what made that legible.**

---

## ⬜ OPEN — the next work, in order

- ⬜ **🟠 PAGE TITLES USE A COLON AND SHOULD NOT.** The sweep turned `Board — Emberhold` into `Board: Emberhold` across **32 route files plus `__root.tsx`'s title/og:title/twitter:title.** A colon implies the left side contains the right. **The convention is a pipe or a middot: `Board · Emberhold`.** Real surface — browser tabs, share cards, link previews. **Free Code, mechanical, do it before it is in anyone's previews.**
- ⬜ **🟠 DELETE THE THROWAWAY TEST BOUNTY.** "Do Not Approve Testing Roll-Over" has served its purpose. Never approved, so Delete is available. **One tap.**
- ⬜ **🟠 `slate.tsx`'s STANDING-DUTIES BLURB READS MUSHY POST-SWEEP.** The em dashes were doing real grammatical work as a parenthetical; commas replaced them and the sentence sags. ⚠️ **Deletion is the likelier right answer than repunctuation — the Slate is showing the standing duties while the copy explains what standing duties are.** Scott's eyes, not a brief.
- ⬜ **🖊️ THE SCREEN COPY PASS — RUNNING.** See parking-lot. **Slate ✅ · Ledger ✅ · Auth ✅ · Campaigns ✅ · Calendar ✅ · Briefing ✅ · everything else unreviewed.**
- ⬜ **`member_admitted` renders as `"Mom · Leo"` — NOW SCOPED.** Two missing enum cases in three identical switches. **Cheap, and no longer a mystery.**
- ⬜ **`logActivity` IS A CLIENT-SIDE BOLT-ON.** Every call site can forget, and one did. **The mechanism fix is the definer RPC writing its own log row.** Migration, so it waits for credits.
- ⬜ **`parent_self_redeem` INSERTS `status='approved'` OUTRIGHT.** An adult redeeming their own embers skips the approval gate. **By design per the code, never written down.**
- ⬜ **The recurrence chip reads `Monthly · 1st` on the Slate.** Confirm the board and create/edit agree.
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building.**
- ⬜ **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- ⬜ **The Briefing's FAB overlaps the Campaigns progress bar.** Sits on top of the São Paulo Trip bar and covers its middle. The board is fine because a card gap sits there. **Layout, Scott's eye.**
- ⬜ **`Testing redemption tracking` and `Testing retired` are both user-visible.** Prod test-object cleanup. ⚠️ **`Testing retired` sits in the Retired section and canon records no un-retire affordance.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE MONTHLY ROLL BRANCH — Aug 1 was an INCIDENTAL exercise, not the verification.** Three past-anchor rows moved correctly and no row duplicated, which is real evidence. ⚠️ **Successor arithmetic and roll-forward are different tests. The clean natural test is 2026-09-01.**
- 🟡 **The kid joiner flow has never been walked end to end by a kid.** ✅ **Unblocked** — the marker dependency cleared today. **First-run fired and completed for Mia on a switch; the JOIN path is still unexercised.**
- 🟡 **`Testing retired` stays retired** once its successor's date arrives. Free, one look.
- 🟡 **The Slate detail panel read IN PROGRESS while the row header read "Done today."** Panel and header may render off different instances. Noticed 07-31, not chased.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** A failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- 🟡 **`/create?recurring=true`** — the Slate's empty-state CTA is verified; the direct-URL half was not separately exercised.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in.** The approve half is exercised; **the wrong-PIN half is not.**
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED EIGHT TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS.
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** · **Avatar render fallback — check the wall** · **Founder tier-tag verification (32 lock / 16 open)** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Wall full end-to-end membrane loop.**

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Pure TypeScript, ten tests, zero Supabase imports, zero credits.** *(`progression.test.ts` carries a pre-existing `tsc --noEmit` error — missing `vitest` types. Surfaced on every Code job on 07-31 and again on 08-01. Clean it with this one.)*
- ⬜ **The engine cannot accept a pre-session constraint.** Scope with the rep-compensation job.
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.**
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by`.** Neither trigger checks the written value names an adult. **Tolerable under the walk-up boundary, not correct.**
- ⬜ **Two derivations of role.** `FirstRunGate` now reads the resolved profile's role; `useMyProfile()` derives from `user_roles`. ⚠️ **Still not interchangeable.** ⚠️ **And the third split stands: `useActiveMember().role` (client, switched profile) vs `has_role(auth.uid())` (server, always the owner). They disagree BY CONSTRUCTION whenever a kid is active.**
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **⚠️ `routeTree.gen.ts` drift is CONFIRMED LIVE.** Every build surfaces it; every agent has to know to throw it away.
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.**
- ⬜ **Deleting a bounty may orphan its calendar event.** **One look.**
- ⬜ **`wall_request_redemption` is called from the Vault and its name lies.** Deliberate debt. **Rename when something else takes that function to Lovable.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** ⚠️ **`wall_request_redemption` checks only that `_profile_id` belongs to the household and is active; it never compares against `auth.uid()`. The Vault now inherits that posture, and so does `mark_first_run_complete`.** Sound under the walk-up boundary; **it is the thing the per-member-auth fork would change.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.**

**Fixed:**
- ✅ **`mark_first_run_complete` profile-scoping — CLOSED 2026-08-01.**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage`.

**Real, open:**
- ⬜ **`quests.approved_by` accepts any in-family profile id, including a kid's.**
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Gate C privacy policy.**

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. ⚠️ *`package-lock.json` and `query_quest.mjs` are sitting untracked in the working tree — confirmed again 08-01.*

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** Gate B. **Deserves its own careful pass.**
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **Page titles → `·` not `:`** · **The screen copy pass (running)** · **The stacked-Pip-voice line on the first setup screen** · **`member_admitted` feed line** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **The Briefing FAB overlapping the Campaigns bar** · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

⚠️ **`useActiveMember()` reads the switched-to profile's own `role` column client-side; every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is.** Every "active member" feature is built on that disagreement. **Three RPCs now take a validated actor/profile id precisely to bridge it** — `approve_redemption`, `wall_request_redemption`, `mark_first_run_complete`. It works, and it is the seam the per-member-auth fork would close.

---

## 🔵 THE BUILD MODEL — holding

- **A PLAN ITERATION COSTS A CREDIT. (NEW — 2026-08-01.)** Plan mode was the right call on a migration — a code revert is not a database revert — but each round trip billed. **Two credits went to one sentence about a null fallback. Review a plan in ONE pass: every objection goes in a single message, or it goes in the next session.**
- **AN INVARIANT MUST BE AN EXPLICIT LINE IN THE BRIEF, NOT INFERRED FROM PROSE. (NEW — 2026-08-01.)** "The read and the write must resolve identically" was written as intent and left the null fallback implicit. **Loose briefs are right for execution latitude and wrong for invariants. State the thing that must be true as its own line.**
- **JAINE'S PUNCTUATION REACHES THE GLASS THE SAME WAY HER RATIONALE DOES. (NEW — 2026-08-01.)** 125 em dashes shipped to users because jAIne writes them into briefs and Lovable renders the phrasing. **Same disease as the escaped subtitles, different tell.**
- **A CANONICAL SNAPSHOT CAN BE CONSUMED BY REAL LIFE. (NEW — 2026-08-01.)** The Aug 1 discriminator was Cade's claims, and Cade did the chores overnight, so the claims moved. **The test survived because the row carried `last done`. Design a control that a working product cannot erase — or make the row carry its own history.**
- **READ THE WHOLE MESSAGE BEFORE ANSWERING IT. (NEW — 2026-08-01.)** jAIne asked whether two chores had been completed overnight in a reply to a message that said they had been. **Second instance of asking a question already answered in the same turn.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH.** When two canon docs disagree, **the one describing SHIPPED BEHAVIOR wins over the one describing PRINCIPLE** — and the contradiction is itself a finding that gets logged.
- **ASK ONE QUESTION BEFORE WRITING A FINDING.**
- **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.** A live debugging exchange wants one paragraph and one question.
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.** ⚠️ **Third instance today: `void profileId` had discarded the id since the marker was written.**
- **A RECON'S FORENSICS AND ITS RECOMMENDATION ARE SEPARATE ARTIFACTS.**
- **GREP CANNOT FIND A BUG THAT ISN'T A STRING.** ✅ **And the recon that answered it proved the inverse is cheap: four files, quoted lines, one afternoon, zero credits.**
- **BRIEF THE RECON TO DISPROVE.** ✅ **Worked today — the brief forbade inferring from a prior report and the answer came back quoted from source.**
- **A SUBTITLE EXPLAINING SOMETHING VISIBLE IS BRIEF TEXT THAT ESCAPED ONTO THE GLASS.** **Default is deletion, not rewording.**
- **A FLAGGED LINE IS A BETTER OUTCOME THAN A GUESSED ONE.** *(New corollary, 08-01: instructing an agent to flag-not-guess produced seven correct abstentions including the en-dash time ranges, which are typographically right and were never the target.)*
- **WHEN A TOOL FAILS SAFE, WIDTH STOPS PREDICTING SPEND.**
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT.** ✅ **Worked on Aug 1 — "somebody did the chores" beat "claims are dropped on roll."**
- **A FILE-BY-FILE SWEEP CAN STILL UNDER-READ A FILE IT VISITED.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED.**
- **A RULE THAT PERMITS TWO WORDS FOR ONE OBJECT IS NOT A DECISION.**
- **ANSWER THE QUESTION THAT WAS ASKED.**
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS.**
- **FIX THE CONTAINER, NOT THE CONTENT.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.**
- **REVERTIBILITY IS A LANE CRITERION, NOT JUST CREDITS.**
- **A 🔴 WITH NO CONSUMER IS NOT A 🔴.**
- **THE FIX WAS RIGHT AND THE REASON WAS WRONG.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** ⚠️ **Three shipped instances now. This is the defining bug class of the codebase.**
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.** ✅ **Enforced explicitly in the marker brief; the old signature was DROPPED.**
- **WHEN THERE IS NO GLASS, VERIFY THE DATA.**
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.**
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.** ⚠️ **Corollary 08-01: and check whether it IS one before assigning severity. "Four divergent implementations" was three identical copies plus a legacy variant.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.** *(Worked six times across two days.)*
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.** · **UNBUNDLE WELDED ASSUMPTIONS.**
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.** ⚠️ **And ask whether YOU are one. `/hearth-log` had a live consumer: Scott.**
- **PRESERVE WHAT ISN'T ENUMERATED.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.**
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.** · **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.** ⚠️ **And verify the file, not the tracking item that describes it. One curl.**
- **A code revert is not a database revert.** Undo schema forward, always. **This is what plan mode is for.**
- **The docs are not the live codebase — and can diverge silently.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **HTTP 200 is not "renders."** **A preview is not prod.**
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis, synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-08-01** — the marker session. First-run marker read/write fixed as one change via a shared resolver; `verbLabel` scoped and closed without a fix; copy pass batch two plus a 125-character em-dash sweep. **August 1 roll-forward passed both legs.** ~2.8 credits, two free Code jobs. `eb93e73` → `fd1d6a8`.
- **2026-07-31** — the redemption-path session. Vault kid-redemption fixed by routing to the existing wall RPC; wall approvals now log; the Bounty coverage grep found a fully unswept onboarding screen; the Slate copy pass. Four Code jobs, zero credits. `e186ff0` → `1ec7af2`.
- **2026-07-30 (night)** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. The Bounty display-string sweep. Five credits.
- **2026-07-30 (late)** — QA/design session. Zero code. Bounty supersedes Quest; the Slate and the Ledger; the roll-forward rule.
- **2026-07-30 (early)** — the redemption `decided_by` fix, verified on live data.
- **2026-07-29 (late)** — the master-spec fold + the first-run completion marker + `943a633`.
- **2026-07-29 (early)** — the install tutorial.
- **2026-07-28** — Forge design session, zero code.
- **2026-07-27 (late)** — the non-creator first run.
- **2026-07-27 (early/mid)** — the `families.timezone` update path.
- **2026-07-26** — table grants closed. Five-screen Pip-guided first run. Signup rebuilt.
- **2026-07-25** — the constitution restructure; the module reframe.
- **2026-07-23** — the household-local date seam.
- **2026-07-21** — the SECURITY DEFINER grant surface; `isActiveQuest` unified.
- **2026-07-19** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.
- **2026-07-16 → 06-26** — roster fixes, admit-on-approval, Claude Code proven as a build lane, avatar roster, XP killed, Vault rails, Lists v1, Campaigns, Calendar, PIN, Quest Log.

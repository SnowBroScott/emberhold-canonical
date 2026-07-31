# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-31 (day)** — *the redemption-path session.* **Zero Lovable credits, four free Code jobs.** A defect nobody had logged took the whole day and it was the right call: **the Vault's Redeem button had never worked for a kid, ever.**

**The headline: a sub-profile can now spend embers from the Vault, wall approvals write to the feed, all three unexercised guarantees from 07-30 are closed on the glass, and the Bounty rename is landed for real — after the grep found a fully unswept onboarding screen.**

**The Vault bug was never a regression.** `redemptions`' INSERT policy requires `requested_by = auth.uid()`. In the shared-session model `auth.uid()` is always the household owner, so a sub-profile could never satisfy it. **The path had failed since it was written and nobody had exercised it.** The wall worked because `wall_request_redemption` is `SECURITY DEFINER` and bypasses table RLS entirely. **Two independent implementations of one insert, and only one of them was ever correct.**

**The fix was free and it deleted the duplication rather than adding to it.** The Vault's kid branch now calls the RPC the wall already used. No migration, no credits, one writer.

**Three verifies closed, one open decision closed, and a copy discipline was born.** Retire, `/create?recurring=true`, and — obliquely — the One-offs cadence rule. `13 need doing` reads as honest, not oppressive; **Scott called it and it is LOCKED, not watched for a week.**

**jAIne was wrong four times today and every one was caught in a turn.** Two are worth carrying: **reasoning off a doc that contradicted another doc in the same fetch**, and **writing a finding off a screenshot without asking one question first.** Both are in the build model.

Last session (prior): **2026-07-30 (night)** — the Slate + Ledger build. Five credits.
Last session (prior): **2026-07-30 (late)** — the QA/design session.
Last session (prior): **2026-07-30 (early)** — the redemption `decided_by` fix.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for every role — and, as of today, a working SPEND path for every role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, tenant isolation verified under live attack, clean function and table grant surfaces, the Slate, the Ledger, a rollover engine that moves rows instead of spawning them.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **⚠️ `master-spec.md` OWES A FULL REGENERATION. UNBLOCKED, DEFERRED TWICE, FIRST IN THE QUEUE.** Six of the 07-30 decisions override its current lines, including a pinned ⚠️ that 07-30 superseded. **jAIne has read ~200 of 669 lines and has twice declined to fake a regeneration.** Dedicated session. **Until then the 07-30 and 07-31 LOCKED decisions govern over the spec's older lines — this is now a standing instruction to every fresh instance, not a note.**

> **`north-star.md` WAS REGENERATED TODAY.** Its membrane parenthetical asserted the wall *"never mints, spends, approves, or edits"* — false about two weeks of shipped behavior, and **it misled jAIne into calling a working wall redemption a membrane breach.** The wall mints, spends and approves through adult PIN by design. Two rename strings folded in the same pass.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The marker's read and write, fixed as ONE change.** `FirstRunGate` reads via `getActiveMemberIdSync() ?? auth.uid()`; `mark_first_run_complete()` writes `WHERE id = auth.uid()`. A kid sub-profile's row id is not any `auth.uid()`, so a kid's write **no-ops silently.** ⚠️ **The two bugs cancel — fixing the read alone produces an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, Lovable lane. ⚠️ **Today's Vault bug was the same class and the same root cause. This is now the second confirmed instance, not a theory.** | The kid joiner flow on shared devices. Gate D. |
| **2** | **🔴 `master-spec.md` FULL REGENERATION.** Dedicated session, needs the full 669-line read. **Deferred twice.** | Every fresh instance reasoning off stale LOCKED lines. |
| **3** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. | Gate B honesty. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

---

## ✅ SHIPPED TODAY — 2026-07-31

*Four Claude Code jobs. Zero Lovable credits. Start hash `e186ff0` → end hash `1ec7af2`.*

### Part A — the Vault redemption fix *(`13c7ab5`)*
- ✅ **`vault.tsx`'s `requestRedeem` now calls `wall_request_redemption` instead of inserting directly.** The direct insert could never satisfy `requested_by = auth.uid()` for a sub-profile.
- ✅ **VERIFIED ON THE GLASS: Mia requested Small treat from the Vault page. No RLS error, "Request sent to an adult," button flipped to Pending, balance held at 61.** Pending does not deduct — `member_spendable()` subtracts approved redemptions only.
- ✅ **Approved from the Vault. Embers moved.** The full kid request → adult approve loop is closed end to end for the first time.
- ⬜ **DEBT CREATED, DELIBERATELY: `wall_request_redemption` is now called from the Vault and its name lies.** Renaming an RPC is a migration; the fix was free. **Rename when something else takes that function to Lovable.**

### Part B — the wall's missing activity write *(`13c7ab5`)*
- ✅ **`wall.tsx`'s approve mutation now calls `logActivity`.** It called the shared `approve_redemption` RPC and never logged; `vault.tsx` always did. **Wall approvals were invisible to the feed, the ticker and the Hearth Log.**
- ✅ **VERIFIED ON THE GLASS: approved Extra screen time from the wall. `Mia redeemed Extra screen time · -25` on the ticker. 61 − 10 − 25 = 26.**
- ⚠️ **The call went in `mutationFn`, not `onSuccess`.** If the log write throws, the UI reports a failed approval that actually committed. **Not verified against how `vault.tsx` does it. One-line check, not worth a round trip alone.**

### Part C — the Bounty coverage grep *(`4f69d33`)*
- ✅ **883 hits across the full working tree.** Exact invocation recorded in the report. `.output/` excluded and disclosed — gitignored build dir, this framework's `dist/`.
- ✅ **`onboarding.first-quest.tsx` WAS COMPLETELY UNSWEPT — eight user-facing strings**, including an h1, a form label, a button and the tab title. **The first bounty a new hold ever creates said "quest" the entire way through.**
- ✅ Two more single misses: `first-run.adult.vault.tsx`, `profile.tsx`.
- ✅ `public/`, the PWA manifest and every `.json`/`.md` that ships: **zero hits, already clean.**
- ✅ **THE RENAME IS LANDED.** Second sweep-miss in two days. **The grep rule is now proven twice, not argued.**

### Part D — the Slate copy pass *(`1ec7af2`)*
- ✅ Eyebrow → **EVERYTHING THAT'S LIVE**. Standing and One-offs subtitles **deleted**. Ledger link → **"The Ledger — what's been done"**.
- ✅ The `subtitle` prop was removed from `StateSection` entirely rather than left as a dead channel.
- ✅ **VERIFIED ON THE GLASS. The Slate and the Ledger are the first two screens through the copy pass.**

### Verifications closed — all free, all glass
- ✅ **RETIRE.** Standing went 5→4, a **Retired · 1** section appeared where none existed, the row reads **"Retired Jul 31,"** Delete was replaced by Retire on an ever-approved bounty. **The section was empty before the test — proving the filter is on `retired_at` and not `archived`.** Both halves of the 07-30 decision are closed.
- ✅ **THE ONE-OFFS CADENCE RULE.** Under the Monthly filter: no One-offs section, no "Filtered out by cadence" empty state. Confirmed incidentally off a screenshot sent for something else.
- ✅ **THE FULL SUB-PROFILE MINT LOOP.** Create → assign to Mia → claim as Mia → complete as Mia → approve as SnowDad → balance lands. **The mint side was never in question and is now evidenced.**
- ✅ **BALANCE MATH IS NOT DUPLICATED.** Every path calls `member_spendable()`. Raised as a currency-integrity risk; **cleared.**
- ✅ **NO TRIGGERS ON `redemptions`.** Raised as a suspect; **cleared.**

---

## ⬜ OPEN — the next work, in order

- ⬜ **🔴 THE MARKER READ/WRITE PAIR.** Critical path #1. Lovable lane, needs credits. **Second confirmed instance of the profile-id-vs-auth-id class.**
- ⬜ **🔴 `master-spec.md` FULL REGENERATION.** Dedicated session.
- ⬜ **🟠 `verbLabel()` LEAKS THE ENUM.** `hearth-log.tsx` special-cases only `bounty_posted`; everything else falls through to `verb.replace("_"," ")`, rendering **QUEST APPROVED** on the glass. **Grep structurally cannot find this — no line spells "quest."** The file is the protected rollback path, so it was correctly untouched. ⚠️ **The real question is whether the same fallback exists in the three surfaces that STAY — wall ticker, Briefing, NotificationBell. Code says their renderers already output "completed." One cheap confirmation.**
- ⬜ **🟠 FOUR INDEPENDENT "VERB → DISPLAY LINE" IMPLEMENTATIONS.** `wall.tsx`, `Briefing.tsx`, `NotificationBell.tsx`, `hearth-log.tsx`. **Third occurrence of the divergence class, after `isActiveQuest` and the two derivations of role. It is why a rename can land in three places and miss the fourth.**
- ⬜ **🟠 THE SCREEN COPY PASS — RUNNING.** See parking-lot. **Slate ✅ · Ledger ✅ · everything else unreviewed.**
- ⬜ **`logActivity` IS A CLIENT-SIDE BOLT-ON.** Every call site can forget, and one did. **The mechanism fix is the definer RPC writing its own log row.** Migration, so it waits for a Lovable session.
- ⬜ **`parent_self_redeem` INSERTS `status='approved'` OUTRIGHT.** An adult redeeming their own embers skips the approval gate. **By design per the code, never written down anywhere.**
- ⬜ **Delete `/quest-log` and `/hearth-log` routes** once the Slate is trusted. ⚠️ **Deleting `hearth-log.tsx` moots the `verbLabel` bug for free — check the other three surfaces first.**
- ⬜ **The recurrence chip reads `Monthly · 1st` on the Slate.** Confirm the board and create/edit agree.
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building.**
- ⬜ **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- ⬜ **`Testing redemption tracking` and `Testing retired` are both user-visible now.** Prod test-object cleanup has a reason to happen sooner. ⚠️ **`Testing retired` sits in the Retired section and canon records no un-retire affordance.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 AUGUST 1 — THE MONTHLY BOARD. THE CONTROL SNAPSHOT IS BANKED.** As of 07-31 07:22 under the Monthly filter: **Mop Downstairs (Due now, claimed by Cade) · Vaccuum Downstairs (Due now, claimed by Cade) · Vacuum Upstairs (Due now, unclaimed) · Brush Chaos (Current, next Aug 1) · Change sheets in Master Bedroom (Current, next Aug 1).** Tomorrow all five read Aug 1 and look identical — **the snapshot is the only thing that distinguishes rolling from spawning.** Three readings: **all five at Aug 1 with Cade's claims intact** = same-row roll worked for monthly; **the top three still at a July date** = the branch didn't fire; **any row appearing twice** = archive-and-spawn regression. ⚠️ **The claim on Cade is the discriminator.**
- 🟡 **🔴 THE MONTHLY ROLL BRANCH — the three past-anchor rows above make Aug 1 an incidental exercise of it.** ⚠️ **Do NOT log it as the branch's verification. Successor arithmetic and roll-forward are different tests. The clean natural test is 2026-09-01.**
- 🟡 **🔴 `submitted` DOES NOT ROLL — STILL NO TEST CASE, AND THE OBVIOUS TEST DOESN'T WORK.** Turning in a bounty and watching it not move today proves nothing: it is due today, today is the current anchor, there is nowhere to move. **The exclusion only exercises when a submitted row is PAST its anchor.** The test: create a throwaway **daily**, claim it, submit it, don't approve it, read it the next morning. **Pass = still at yesterday's date, still submitted.** Then delete it — never approved, so Delete is available.
- 🟡 **`Testing retired` stays retired** once its successor's date arrives. Free, one look.
- 🟡 **The Slate detail panel read IN PROGRESS while the row header read "Done today."** Panel and header may render off different instances. Noticed on the retire test, not chased.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** Compare against `vault.tsx`.
- 🟡 **Feed verb history.** ⚠️ **CONFIRMED ON THE GLASS, NOT A QUESTION ANYMORE** — `BOUNTY POSTED` and `QUEST APPROVED` in one scroll. Cause is `verbLabel`, above.
- 🟡 **`/create?recurring=true`** — ✅ **the Slate's empty-state CTA is verified.** The direct-URL half was not separately exercised.
- 🟡 **The Vault-page approval path post-migration.** ✅ **Closed today** — approved a pending kid redemption from the Vault page.
- 🟡 **The kid joiner flow has never been walked by a kid.** Blocked on critical-path #1.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in.** approve → PIN → wrong/kid PIN mints nothing → correct PIN commits. **The approve half is now exercised; the wrong-PIN half is not.**
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED SEVEN TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS.
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** · **Avatar render fallback — check the wall** · **Founder tier-tag verification (32 lock / 16 open)** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Wall full end-to-end membrane loop.**

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Pure TypeScript, ten tests, zero Supabase imports, zero credits.** *(`progression.test.ts` carries a pre-existing `tsc --noEmit` error — missing `vitest` types. Surfaced on every Code job today. Clean it with this one.)*
- ⬜ **The engine cannot accept a pre-session constraint.** Scope with the rep-compensation job.
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.**
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by`.** Neither trigger checks the written value names an adult. **Tolerable under the walk-up boundary, not correct.**
- ⬜ **Two derivations of role.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. ⚠️ **Not interchangeable.** ⚠️ **Today's recon confirmed a third split of the same shape: `useActiveMember().role` (client, switched profile) vs `has_role(auth.uid())` (server, always the owner). They disagree BY CONSTRUCTION whenever a kid is active.**
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **⚠️ `routeTree.gen.ts` drift is CONFIRMED LIVE.** Every build surfaces it; every agent has to know to throw it away.
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.**
- ⬜ **Deleting a bounty may orphan its calendar event.** Creating one writes an `EVENT CREATED` row; the deleted `Testing retired` was never checked against the calendar. **One look.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** ⚠️ **Now mechanically explained: `wall_request_redemption` checks only that `_profile_id` belongs to the household and is active. It never compares against `auth.uid()`. Today's fix routes the Vault through that same function, so the Vault inherits the same posture.** Sound under the walk-up boundary; **it is the thing the per-member-auth fork would change.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.**

**Fixed:**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage`.

**Real, open:**
- ⬜ **`quests.approved_by` accepts any in-family profile id, including a kid's.**
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Gate C privacy policy.**

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. ⚠️ *`package-lock.json` is sitting untracked in the working tree — noticed by Code today, unrelated to any job.*

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** Gate B. **Deserves its own careful pass.**
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — now visible to users in the Ledger AND the Slate's Retired section.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The marker read/write pair.** CRITICAL PATH #1.
- ⬜ **`master-spec.md` full regeneration.** CRITICAL PATH #2.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The screen copy pass (running — see parking-lot)** · **The stacked-Pip-voice line on the first setup screen** · **`member_admitted` feed line** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

⚠️ **Today gave this boundary a mechanism, not just a posture.** `useActiveMember()` reads the switched-to profile's own `role` column client-side; every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. **The client thinks a kid is acting; the database always thinks the owner is.** Every "active member" feature is built on that disagreement. It works, and it is the seam the per-member-auth fork would close.

---

## 🔵 THE BUILD MODEL — holding

- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH. (NEW — 2026-07-31.)** north-star said the wall never spends; status listed wall-approve-with-PIN as a pending verify. jAIne fetched both in the same session, reasoned off the wrong one, and called shipped behavior a membrane breach. **When two canon docs disagree, the one describing SHIPPED BEHAVIOR wins over the one describing PRINCIPLE — and the contradiction is itself a finding that gets logged, not silently resolved.**
- **ASK ONE QUESTION BEFORE WRITING A FINDING. (NEW — 2026-07-31.)** jAIne wrote a full "the log lies at request time" finding off a screenshot taken before Scott approved the redemption. **"Did you approve it?" was four words and would have killed it.** Scott: *"seems like you are working really hard for little value."*
- **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER. (NEW — 2026-07-31.)** Scott reported skipping 85–90% of several replies. **Multi-section responses in a fast diagnostic loop are not thoroughness; they are a slower channel.** Match the register: a live debugging exchange wants one paragraph and one question.
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS. (NEW — 2026-07-31.)** jAIne opened with "regression" and reached for the most recent migration. The path had never worked once. **Ask whether anyone has ever exercised it before asking what changed.**
- **A RECON'S FORENSICS AND ITS RECOMMENDATION ARE SEPARATE ARTIFACTS. (NEW — 2026-07-31.)** Today's recon was flawless on mechanism and its recommended fix would have broken the wall for every kid — it assumed the older policy was correct and the newer RPC deviant. **Read the evidence, discard the conclusion, decide separately.**
- **GREP CANNOT FIND A BUG THAT ISN'T A STRING. (NEW — 2026-07-31.)** `QUEST APPROVED` renders from `verb.replace("_"," ")`. No line spells "quest." **A textual sweep has a structural blind spot: anything the code assembles at runtime.**
- **A SUBTITLE EXPLAINING SOMETHING VISIBLE IS BRIEF TEXT THAT ESCAPED ONTO THE GLASS. (NEW — 2026-07-31.)** "Everything with a future," "one line, forever," "here until they're approved" were jAIne's own design-rationale phrasings, rendered by Lovable as UI. **The app explaining its own architecture to a nine-year-old. Default is deletion, not rewording.**
- **WHEN A TOOL FAILS SAFE, WIDTH STOPS PREDICTING SPEND.** Lovable stops rather than half-landing; the expensive prompt is the *ambiguous* one, not the wide one.
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT.**
- **A FILE-BY-FILE SWEEP CAN STILL UNDER-READ A FILE IT VISITED.** ⚠️ **Confirmed twice now — and the second time the sweep had never opened the file at all.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED.** ⚠️ **A full day of work on zero credits, four jobs, four commits.**
- **A RULE THAT PERMITS TWO WORDS FOR ONE OBJECT IS NOT A DECISION.**
- **ANSWER THE QUESTION THAT WAS ASKED.**
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS.**
- **FIX THE CONTAINER, NOT THE CONTENT.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.** *("Vaccuum Downstairs" survived a second sweep. Still correct.)*
- **REVERTIBILITY IS A LANE CRITERION, NOT JUST CREDITS.**
- **A 🔴 WITH NO CONSUMER IS NOT A 🔴.**
- **THE FIX WAS RIGHT AND THE REASON WAS WRONG.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** ⚠️ **Second confirmed instance today. This is the class behind the marker bug AND the Vault bug.**
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.**
- **WHEN THERE IS NO GLASS, VERIFY THE DATA.**
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.** ⚠️ **Corollary from today: two paths where one fails loudly and one succeeds silently is the cheap version — the loud one is the lucky one.**
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.** *(Worked four times today — every job reported the hash before reading.)*
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.** · **UNBUNDLE WELDED ASSUMPTIONS.**
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.**
- **PRESERVE WHAT ISN'T ENUMERATED.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.**
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.** · **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.** · **A prompt's DO-NOT-BUILD list is not self-enforcing.**
- **A code revert is not a database revert.** Undo schema forward, always.
- **The docs are not the live codebase — and can diverge silently.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.**
- **HTTP 200 is not "renders."** **A preview is not prod.**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

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

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-27 (late)** — *the joiner-flow session. The largest unstarted item on the board was scoped, built, and published dark.* **Net code delta: two commits (`f580162`, `369164e`). Net recon delta: one three-question read-only Code job. Net doc delta: status and parking-lot replaced, ten `decisions.md` entries. Lovable credits spent: zero.**

**The non-creator first run exists.** Eight screens, two role-split step arrays, an arrival gate, and route guards — all riding the existing `SetupShell`. **It is inert by design and nobody has been through it in production.** The completion marker it depends on is a `profiles` column that does not exist yet.

**A live regression was created and caught inside the same session.** The route guard added to protect `/onboarding/*` from URL-walking bounced *every* new hold out of the 07-26 activation flow — empty board, no first quest, no handoff. Caught in dev, fixed at `369164e`, never published broken. **The cause was one predicate serving two consumers that need opposite null behavior:** "inert" means *don't fire* for an arrival gate and *don't block* for a route guard.

**Signup is verified on the glass and it is better than it was.** Cold create-path signup walked end to end: the verification link lands straight in hold creation, the flow runs, the hold gets built. **Signup check #1 is closed, and it doubles as proof the `/onboarding/*` regression is gone.** The 07-26 decision to delete the pre-verification stash is validated in use — removing a feature made the product materially faster.

**Auth email is downgraded from critical path #1 to tracked-not-worked.** Renaming the Lovable project to Emberhold fixed sender identity; **one of six providers still spams it.** The first artifact a stranger receives is now *from Emberhold*, which was the load-bearing half of the Gate B criterion.

**The timezone update path shipped, and the hold-settings hierarchy pass shipped.** The timezone decision entry stays **DRAFT** — its verification can only be performed from a device in a non-Pacific timezone and nobody has one.

Last session (prior): **2026-07-27 (early)** — a design and recon session that shipped no code and right-sized two of the largest items on the board. `append_activity` downgraded; Forge scoped to the Draper household.

Last session (prior): **2026-07-26** — table grants closed, `anon` at zero across fifteen tables. Five-screen Pip-guided first run shipped and cold-walked.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for creators and a built-but-dark one for everyone else.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model with a working update path, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a five-screen creator first run, and eight non-creator first-run screens behind an inert gate.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness is the sole module, it is not built, and it is scoped to the Draper household.**

> **`master-spec.md` owes FIVE folds.** The creator onboarding flow · the signup posture · the write-once timezone and its heal · Part II's rescoping to household-only · **and the non-creator first run, including the completion-marker seam and the arrival-state rule.** Plus five corrections found on the 07-27 read: the `actor_label` derive-from-`auth.uid()` prescription is now a killed regression, the "fourth activeness surface under investigation" note is dead, the `set_enabled_modules` "nothing has ever written that value" claim is false, the Onboarding section still describes the empty-board gap as the top structural problem, and the COPPA "kids have no auth identity" shield is false for a join-by-code minor. **jAIne has read the current version — a real regeneration is available and is the next scheduled work.**

> **`north-star.md` is current.** One sentence is drifting: *"the auth email is now load-bearing in a way it wasn't"* was written when the entire signup path depended on it. Sender identity is fixed and only deliverability remains. Fold on the same pass.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The first-run completion marker.** A `profiles` column plus a write path. **The eight screens are dark until it lands.** ⚠️ **Answer the RLS question before scoping the build: can a member UPDATE their own `profiles` row?** If not, this is a column *plus* an RPC, not a column. Lovable lane. | Gate D. Activation for everyone who isn't the hold creator. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.** Gate B's exit criterion is partially met — the first email a stranger receives *is* from Emberhold.

**Off the critical path and now household-scoped: Forge.** Design is a rest-period activity and costs no build lane. Build work waits for V1.

---

## 🟢 CLOSED / RESOLVED — 2026-07-27 (late)

### The non-creator first run — scoped

- ✅ **The shape is settled: same `SetupShell`, a second step registry, and the flow writes nothing but a completion marker.** Not a variant of the creator flow and not a separate artifact. The shell is neutral machinery — recon found **zero** creator-only writes and no reference to role, creation, or session state in `setup-flow.ts` or `SetupShell.tsx`.
- ✅ **Reusing the creator screens was never viable, and the reason is permissions, not the shell.** Of the three screens that write, **two are parent-gated** — `add-family` (`create_adult_profile` + `enforce_profile_insert_rules`) and `stock-vault` (RLS `"Parents create rewards"`). `first-quest` is ungated. A kid walked through `stock-vault` eats an RLS denial behind a friendly mascot.
- ✅ **One build serves both role flavors because the flow writes nothing.** No writes, no permission surface, nothing to differ on. The flavors are a step array and copy.
- ✅ **The trigger seam is pend→active, checked as ARRIVAL STATE.** Not the create-vs-join fork in `auth.tsx` — a joiner is still `pending` there and lands on `PendingWaitingScreen`. Not an event on the transition either — admission usually happens with the app closed.
- ✅ **Both doors are in scope.** Join-by-code and profile-switch. **A 13-year-old with a phone and a join code is normal traffic, not an edge case** — the join form has an adult/kid selector and a teenager will use it.
- ✅ **PIN is EXPLAINED, never collected.** The fail-closed intercept at profile switch-in already owns collection.
- ✅ **"Replay the intro" does NOT fold in.** Option (a) — the sheet stays, nothing points at the flow. The modality is wrong in both directions.

### Built and published — dark

- ✅ **`f580162`** — `first-run.ts` (the two-function seam + shared guard), `first-run-flow.ts` (both step registries), eight screens under `/first-run/adult/*` and `/first-run/kid/*`, `SetupShell` index/total support, the arrival gate in `_authenticated/route.tsx`, route guards on `/onboarding/*` and `/first-run/*`, and the creator handoff marking the same marker.
- ✅ **`369164e`** — `FIRST_RUN_MARKER_AVAILABLE = false` short-circuits `redirectIfFirstRunDone()` before any auth call or redirect. **This closed the regression and, as a side effect, made `/first-run/*` reachable by URL.**
- ✅ **Both paths walked on the glass.** Adult and kid arrays render, advance, and hand off. **No screen frames role as status** — the copy constraint landed.
- ✅ **The PIN screen is correct in tone and content.** Explains, collects nothing, tells the user there is nothing to do here.
- 🔵 **Verification was available the whole time and jAIne said it wasn't.** The `369164e` fix made the routes reachable; jAIne proposed a production stub-flip instead, which would have ambushed all thirteen households with an unskippable walkthrough.

### Recon — three questions, all answered

- ✅ **Q1: no first-run marker exists anywhere.** Not on `profiles`, not in any migration, not in any RPC. Client-side candidates are all unrelated: `emberhold:first-quest-doorway-dismissed` (sessionStorage, banner-only), `emberhold:active-member`, `emberhold:pending-invite-code`.
- 🔵 **`FirstQuestDoorway`'s flagless derived gate does not transfer.** It works because the gate and the goal are the same fact (does a quest exist). First-run teaches four things and none is a queryable fact — **and a derived gate cannot remember a skip**, which re-fires the flow forever and inverts the one-directional decision.
- ✅ **Q2: nothing surfaces the confirmed role to a joiner, and the evidence is destroyed.** `admit_pending_member` sets `requested_role = NULL` in the same UPDATE that sets the confirmed `role`. `activity_log` has no role column. The admitting parent's success toast fires on *their* client.
- ✅ **Q3: quest-creation discoverability — HYPOTHESIS DISPROVED.** `campaign.$id.tsx` gates its create affordance on `isParent`; `BottomBar`'s FAB and `QuickAddTray` carry no role condition at all. **A kid sees "New quest" on the global FAB and does not see it on a campaign.** Closes a carried recon line.

### Signup

- ✅ **Signup check #1 CLOSED.** Cold create-path signup, verification link, straight into hold creation. **Faster and less cumbersome than the pre-verification build.** Doubles as proof the `/onboarding/*` regression is gone.

### Shipped earlier the same day

- ✅ **The `families.timezone` update path.** `timezone_confirmed_at`, the parent-gated RPC, the silent one-time heal, settings display + reset.
- ✅ **The hold-settings hierarchy pass.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 Signup checks #2 and #3.** (2) Cold join-path signup — no PIN field, no error toast, lands as pending. (3) Original-tab path — sign up, ignore the email, press "I confirmed," sign in; **should land on finish-setup identically to check #1.**
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** The decision entry stays DRAFT. Verification requires hardware nobody has. **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in.** The code writes the PIN-verified adult (`adultId`), not the session owner. Test against that expectation. Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits and balance moves.
- 🟡 **Monthly post-fix is technically unexercised.** Failure window is approving on the last evening of a month after UTC has rolled. Narrow.
- 🟡 **STALE chip predicate.** Likely `due_date < today`, which the two stranded past-due weeklies would fully explain.
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED THREE TIMES.** `SQLSTATE 42501` = broken grant (FAIL); any application-level error = PASS. *Sonnet · auto-accept OFF · read-only.*
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built. **The trigger has not fired.**
- 🟡 **The ember progress trail** — visual success criterion, Scott only.
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member. **Check the wall specifically.**
- 🟡 **Founder tier-tag verification.** Flip gate ON, confirm **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. **Now the test case for the timezone heal.**
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — new from 2026-07-27 (late)

- ⬜ **🔴 `FirstRunGate` reads the ACCOUNT HOLDER, not the switched-into profile.** `_authenticated/route.tsx` resolves `profile` from `.eq("id", data.user.id)` and hands its `id` and `role` to the gate. **On a shared device a kid switching in is checked against the account holder's marker and the account holder's role.** Door two — the profile-switch kid — is *not* handled. `redirectIfFirstRunDone` uses `getActiveMemberIdSync()` and is correct; the gate that actually fires the flow is not. **Fix before the marker column lands or the kid flow never runs for the people it was built for.**
- ⬜ **🔴 THE MARKER TRIPWIRE — one change, three parts.** `FIRST_RUN_MARKER_AVAILABLE`, `hasCompletedFirstRun`, and `markFirstRunComplete` must go live together. **Flip the constant without swapping the bodies and `hasCompletedFirstRun` still returns a literal `true`, the guard goes live against every walkthrough route, and the `/onboarding/*` regression returns.** This belongs in the build brief, not in a code comment.
- ⬜ **Two derivations of role now exist in the app.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. `admit_pending_member` writes both so they agree today. **This is the `isActiveQuest` defect pattern re-appearing** — divergent predicates for one concept, agreeing by coincidence of a single writer.
- ⬜ **Redemption approval must move to match the PIN screen's promise.** Pip now tells users the PIN keeps *"an approval or a redemption tied to the person who actually signed off on it."* True for quests (`approved_by = adultId`), false for redemptions (`decided_by = auth.uid()`). **Deadline: the string has to be true before the arrival gate goes live — same change as the marker column.**
- ⬜ **The optimistic `true` will flash.** When the marker is real, `FirstRunGate` initializes `firstRunDone = true`, so a joiner sees the board for a frame before being pulled into the walkthrough. Cosmetic, fixable then.
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.** Same class as `approved_by`/`decided_by` — two surfaces disagreeing about one rule. **The curriculum decision still holds** (absence, not gating); this is a consistency question, not a permission one.
- ⬜ **`routeTree.gen.ts` was hand-edited, and there is toolchain drift underneath it.** The locally-installed `@tanstack/router-plugin` produces stricter codegen than whatever generated the committed file, surfacing ~40 pre-existing `search`-param errors on an untouched baseline. **Probably self-healing** — the route files exist and Lovable's codegen will pick them up. **But local `npm run dev`/`build` is now a trap.**
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.** A milestone-significance verb with no case in either feed consumer's switch (`hearth-log.tsx`, `NotificationBell.tsx`), falling through to the `default` branch.
- ⬜ **The COPPA shield in `master-spec.md` is false for a real path.** The spec claims kids-have-no-auth-identity as the COPPA advantage — no kid email, no direct collection. **A minor joining by code has an email, a password, and an auth row.** At 13 that's fine; a 10-year-old walks the identical path and nothing asks. Gate C names COPPA, so this is not new work — **the sentence is wrong and must be qualified in the fold.**

## ⬜ OPEN — carried

- ⬜ **`approved_by` vs `decided_by` — RESOLVED IN DIRECTION, UNBUILT.** Quest approval's behavior wins; redemption aligns to it. See OPEN above for the deadline.
- ⬜ **What does `actor_label` mean?** `actor_id` is server-derived and truthful; only the rendered name is client-supplied. The obvious fix is a regression. **Design call, needs Scott.**
- ⬜ **`enabled_modules` has no consumer.** Sixth session carrying it. Named future consumer is the gated `/forge` route.
- ⬜ **`master-spec.md` owes FIVE folds.** See above.
- ⬜ **Unapproved weekly and monthly quests never roll forward.** **Two stranded right now** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted).
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY.** Approval is the real gate. What changes is the curriculum.
- ⬜ **`progression.ts` has no progression axis except load.** The Forge differentiator is unbuilt in the one asset that already existed. Claude Code job, zero credits.
- ⬜ **The Smith machine offset is assumed, not measured.** A wrong offset silently corrupts every Smith e1RM forever.
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag.
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.** "Take out the trash" carries a stale pre-migration future `due_date`.
- ⬜ **Quest Log applies no `due_date` filter — by design or by omission?**
- ⬜ **Module navigation is unresolved.** Seven tabs is one past mobile comfort. **Not "add another tab."**
- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the standard is QUEST POSTED.

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** *(Minor: `confirm()` copy is inaccurate.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'`/`status='pending'`; real tier set by parent-gated `admit_pending_member`. **Re-confirmed a fourth time this session from the migration source.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.**

**Fixed:**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage` (closed by deletion).

**Real, open:**
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **`approved_by` accepts any adult from any parent session.** Direction resolved; build owed.
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — unreachable from this connection, platform-scoped.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Downgraded to tracked-not-worked. One of six providers still spams. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.** **Pattern: deleting a quest does not clean up redemptions it funded.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The first-run completion marker.** CRITICAL PATH #1.
- ⬜ **`FirstRunGate` profile-switch bug.** Ships with the marker or the kid flow is dead on arrival.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **A GUARD THAT PROTECTS A ROUTE CAN BREAK THE FLOW THAT USES IT. (NEW.)** The `/onboarding/*` guard was added to stop URL-walking and bounced every new hold out of activation. **Two instructions, each individually correct, jointly catastrophic.** Before adding a guard, name every consumer of the route it guards.
- **"INERT" IS NOT ONE BEHAVIOR. (NEW.)** A stub feeding both a gate and a guard needs opposite null semantics — *don't fire* versus *don't block*. One predicate, two meanings, and the default that is safe for one is maximally unsafe for the other.
- **DELETING A FEATURE MADE THE PRODUCT BETTER. (NEW.)** The pre-verification stash was removed to fix retyping; the reported outcome is a faster, more fluid signup. **The shortcut was costing more than it saved.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT. (NEW.)** jAIne asserted the marker keys to the switched-into profile after reading one file and inferring the other. It was true in `first-run.ts` and false in the gate that actually fires. **Read the consumer, not the helper.**
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.**
- **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.** Re-read the reasoning, not the ranking.
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.**
- **BUILD THE FRAME BEFORE THE CONTENT.**
- **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.**
- **A prompt's DO-NOT-BUILD list is not self-enforcing.**
- **jAIne must not prescribe a check the frontend cannot perform.**
- **State a hypothesis as a hypothesis, then read before asserting.**
- **A code revert is not a database revert.** Undo schema forward, always. **Corollary, newly useful: a commit with zero `supabase/` changes is fully revertible by a version restore.**
- **The docs are not the live codebase — and can diverge silently.**
- **Hand-applied DB changes are forbidden.**
- **A Code job isn't done until the artifact is observable from outside the agent.** ⚠️ **jAIne cannot verify `theemberhold` hashes — the repo is private and `api.github.com` returns 403.** A commit card in the client is the external observation.
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.**
- **Sweep the class before fixing the instance.**
- **A defensive layer is not verified by the migration that adds it.** **HTTP 200 is not "renders."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.** design-only · design + Lovable · design + Code · full. **Credits and hands are independent switches.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-27 (late)** — the non-creator first run: eight screens, two step arrays, arrival gate, route guards, the marker seam. Published dark. A live regression created and caught in the same session.
- **2026-07-27 (early/mid)** — the `families.timezone` update path and the hold-settings hierarchy pass. Two read-only Code recons.
- **2026-07-26** — table grants closed; `anon` at zero across fifteen tables; default privileges fixed at the mechanism. Five-screen Pip-guided first run. Signup rebuilt; the `pending_setup` stash and its plaintext PIN deleted.
- **2026-07-25 (late)** — the constitution restructure. `master-spec.md` fully regenerated.
- **2026-07-25 (early)** — the module reframe. An eight-table parallel tenancy built and dropped the same night.
- **2026-07-23** — the household-local date seam. `families.timezone` + `household_today()`.
- **2026-07-21 (late)** — the SECURITY DEFINER grant surface.
- **2026-07-21 (evening)** — recurring-quest visibility. `isActiveQuest` unified.
- **2026-07-19** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED. Wall display-mode fine-tune. Avatar transport end-to-end.
- **2026-07-16** — roster "no members" root-caused. Pending→admission routing fixed.
- **2026-07-15** — admit-on-approval shipped. Live privilege-escalation fixed.
- **2026-07-14** — admit-on-approval data layer. Recurrence reworked to fixed calendar anchors.
- **2026-07-12** — Claude Code proven as a build lane. Auth hardening.
- **2026-07-11** — engine daily respawn verified. Avatar roster regenerated.
- **2026-07-10** — engine auto-approval, edit-form parity. XP killed. Vault rails.
- **2026-07-03 → 06-26** — Avatar Overhaul designed. Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, Campaigns, Calendar, the Vault, PIN, Quest Log.

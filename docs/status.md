# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-08-03** — *the decision session.* **ZERO Lovable credits. Four Code jobs, four decisions, one ratification, three board corrections, three commits.**

**The headline: critical path #1 is decided, and Phase 1 is now three items, none of which are code.** The own-session versus per-member-auth fork resolved to **own-session, LOCKED**, which unblocked four items by reclassifying them rather than fixing them. Avatar roster transport turned out to be **already shipped and had been sitting on the critical path for weeks**. What remains before Gate B is backup posture, auth email deliverability, and the Android install prompt. Two of those are errands and one is a DNS purchase.

**PARENT AND KID ARE RETIRED FROM THE GLASS. THE PAIR IS KEEPER AND KIN.** Locked and shipped across three commits, zero credits, zero schema. `profiles.role` still stores `parent` and `kid`, exactly the way identifiers kept saying `quest` after Bounty won. ⚠️ **All three commits are unverified on the glass except two screenshots from the first.**

**THE RENAME WAS THE OCCASION, NOT THE POINT.** Two shared label maps now exist in `src/lib/quest-helpers.ts`: `ROLE_LABEL` and `AUDIENCE_LABEL`, alongside the existing `STATUS_LABEL` and `TIER_LABEL`. Eight copy-pasted role ternaries and three copy-pasted picker arrays now read from them. **The next label change to either concept is one line in one file.**

⚠️ **THE BOARD WAS WRONG TWICE TONIGHT, BOTH TIMES IN THE DIRECTION OF PESSIMISM.** Avatar transport was listed as Gate A's tail while all 48 avatars were live, pickable, and rendering everywhere. The onboarding add-member avatar picker was described as absent by construction; it is present and skippable. **Both were corrected by asking Scott rather than by building anything.**

Last session (prior): **2026-08-02** — the assessment session. ~1.9 credits, four Code jobs, one migration.
Last session (prior): **2026-08-01 (late)** — the free session. Zero credits, service worker shipped.
Last session (prior): **2026-07-31 (night) → 2026-08-01 (early)** — the marker session. ~2.8 credits.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## 🧭 THE REFRAME — WHAT "READY" MEANS NOW

**Phase 2 is Stripe, the Founding Guildhall, refunds, tax, COPPA, and the privacy policy.** It is real, it is next, and it is explicitly **not** part of getting Emberhold finished. It was one row on the critical path pretending to be one task, and it is at least fifteen.

**Phase 1 is "a complete and ready Emberhold," and its definition is Gate B's exit criterion, verbatim:** a stranger onboards unassisted, tenant isolation holds, the first email they receive is from Emberhold and reaches the inbox, and offline shows a themed shell.

**Against that bar, Phase 1 is now three items and a test.** Backup posture, auth email deliverability, the Android install prompt, and a stranger onboarding unassisted. **Nothing on that list is a build.**

⚠️ **THE BOARD CANNOT TELL THE DIFFERENCE BETWEEN "NEVER DONE" AND "DONE SO OFTEN NOBODY NARRATES IT."** This failed twice on 08-03 and three times on 08-02. **It is now the most reliable defect on this board.** The fix is free: jAIne asks before carrying an item forward, and Scott says normal, never happened, or don't care. ⚠️ **jAIne read the manifest instead of asking, and canon beat the person who uses the app daily. That is the wrong precedence and it is written into the build model below.**

---

## Where the platform is

**Structurally complete, published, installable on iOS, with a working activation path, a working spend path for every role, and the full 48-avatar roster live on the glass.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, avatars, a household-local date model, tenant isolation verified under live attack, clean function and table grant surfaces, the Slate, the Ledger, a rollover engine verified on a real month boundary, a registered service worker, a server-validated quest approver, and **as of tonight a role vocabulary that does not assume a nuclear family.**

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> ⚠️ **RESIDUAL, UNCHANGED:** roughly 400 lines of master-spec, Part II (Forge), the onboarding sections and the schema detail, remain unread since the 07-29/07-30 folds. **A full cold read is worth doing eventually; it is not urgent.**

---

## 🔴 THE CRITICAL PATH — PHASE 1 ONLY

| # | Item | Blocks |
|---|---|---|
| **1** | **⬜ BACKUP POSTURE.** ⚠️ **Lovable could not confirm PITR, backup schedule, or retention.** Self-service export exists at **Cloud → Advanced settings → Export data**. **Live user data across thirteen accounts, no confirmed recoverable backup.** **Zero backups to one backup is the largest single risk reduction available on this board and it costs a click.** | Gate B. Distribution. |
| **2** | **⬜ AUTH EMAIL DELIVERABILITY.** One of six providers still spams. **Deferred by choice: Scott is not spending on DNS until the end.** Tracked, not forgotten. | Gate B. Every stranger signup. |
| **3** | **🟡 THE ANDROID INSTALL PROMPT.** Scott's, in progress on his own schedule. **Chrome proper, not Fully Kiosk.** | Gate B. The wall device. |

**Gone from this table tonight:** the own-session fork (decided, see below) and avatar roster transport (shipped, see below).

**Phase 2 (Stripe, Guildhall, COPPA, privacy policy, refunds, tax) is deliberately absent from this table.**

---

## 🔒 THE FORK — DECIDED 2026-08-03

**OWN-SESSION. LOCKED.** The shared-session model with client-side member switching stays, permanently, with one named reopen trigger.

**The crux, and it is the whole decision in one sentence: in the shared-session model no server-side check can ever distinguish a Kin from a Keeper, because the only evidence of who is acting is client-supplied.** `auth.uid()` is always the owner. `useActiveMember()` is a localStorage key. There is no third source of truth and one cannot be built from the two that exist. **Every attempt to close the audience reads inside this model ends in the same circle: the RPC validates that the passed profile belongs to the household, and a Kin with devtools passes a Keeper's profile id.**

**Per-member auth was rejected on four grounds, all of them real:**
1. **Kids have no email.** It requires building a credential path, and `kid-auth` is already DECLINED. It is a reopen of a settled call, not a new one.
2. **The wall does not benefit.** The wall is one device any member walks up to and it can never have a per-member session. `wall_request_redemption` and the validated-actor-id pattern survive per-member auth unchanged. **It does not replace the pattern, it adds a second authority model alongside it.**
3. **COPPA gets worse, not better.** Per-member auth creates accounts for under-thirteens, which is the literal trigger. Own-session collects nothing from Kin because Kin have no accounts. **It makes a Phase 2 item that is already fifteen rows more expensive.**
4. **It degrades the daily loop.** Switching becomes sign out and sign in, on the shared family device, in the path Scott and May use constantly.

**THE REOPEN TRIGGER: flat or peer holds.** Roommates, a nanny, a non-custodial parent. The walk-up boundary is only sound because physical possession of the device equals household authority. That is true of a family and flatly false of roommates. **Already parked in LATER, so the trigger is already written down.** Nothing at Gate B or Gate E forces the other branch.

**WHAT THE DECISION RECLASSIFIED, ALL FOUR:**
- 🔵 **Kin read `parents_only` quest details — ACCEPTED, PERMANENTLY.** Unenforceable under own-session.
- 🔵 **Kin read `adults_only` reward names and costs — ACCEPTED, PERMANENTLY.** Same class.
- 🔵 **"Adult PIN lock isn't tied to real permission checks" — the "conditionally" comes off.** Unconditional accepted design.
- 🔵 **"Any member can submit a redemption attributed to another member" — the "conditionally" comes off.** Same.

⚠️ **THE LAST TWO SIT AS IGNORED IN THE LOVABLE PANEL WITH A FOOTNOTE SAYING THEY REVIVE IF THE FORK GOES THE OTHER WAY. IT DID NOT. THE FOOTNOTE COMES OFF.** And the first two were **deliberately left visible on the panel pending this call**; that reason has expired and they should be marked ignored with the fork reasoning attached. **Leaving them open is how the panel goes back to training the reader to skim, which is the disease 08-02 cured.**

---

## ✅ SHIPPED — 2026-08-03

### Keeper and Kin *(three commits, zero credits, frontend only)*

**`9c83fc4`** — `ROLE_LABEL` extracted into `quest-helpers.ts` beside `STATUS_LABEL` and `TIER_LABEL`; eight copy-pasted role ternaries across `auth.tsx`, `Briefing.tsx` (×2), `onboarding.add-family.tsx`, `profile.tsx` (×3) and `AppShell.tsx` now read from it. Thirteen one-off prose strings reworded. 10 files, +38/-31.

**`e8223f4`** — `AUDIENCE_LABEL` extracted the same way; all three copy-pasted audience pickers (`create.tsx`, `quest-log.tsx` EditPanel, `slate.tsx` EditPanel) now read from it. `QuestCard.tsx`'s `AudienceBadge` made three-way. 5 files, +22/-12.

**`f9f731e`** — the word "adult" swept to "Keeper" across 24 prose and label sites in nine files including the wall's nine independent strings; the `Available to anyone` line deleted; the Board's two overclaiming headings corrected; the Vault's reward-audience labels relabeled. 12 files, +40/-40.

- ✅ **47 typecheck errors before and after, all three times.** No new error class in any commit.
- ✅ **Zero identifiers touched.** `profiles.role` still stores `parent` and `kid`. The quest audience enum still stores `anyone` / `parents-only` / `kids-only`. The reward audience enum still stores `household` / `adults_only`. `AdultsOnlyTag` keeps its component name.
- ⚠️ **THE STORED VALUE IS `parents-only`, NOT `adults-only`.** jAIne's brief guessed and said so; the agent verified before touching anything. **The database says `parents-only`, the code says `AUDIENCE_LABEL`, the glass says Keepers only.** Deliberate, exactly like quest surviving Bounty. **Written down so a future instance does not trip on it.**
- ⚠️ **UNVERIFIED ON THE GLASS** except two screenshots from the first commit showing the Board roster and the profile screen. **The Vault took sixteen lines across seven strings and the wall took nine. Neither has been looked at.**

### Defects fixed as a side effect of the rename

- ✅ **`QuestCard.tsx:86` never read `quest.audience` at all.** It rendered "Available to anyone" whenever there was no assignee, **so a `parents-only` bounty could display the Keepers only badge and a line saying it was available to anyone, on the same card.** Deleted, which fixed the contradiction and returned a line of vertical height on every unrestricted card.
- ✅ **`board.tsx:149` and `:197` claimed "anyone can claim"** above lists that include restricted bounties for a Keeper viewer. Both now read "Open bounties". ⚠️ **Line 149's kicker may now stutter against the `<h1>` directly below it. Scott's eye, one look.**
- ✅ **`auth.tsx:242` and `profile.tsx:459` guard the same rule and had already drifted apart**, because the earlier search matched "parent" and this copy said "adult". Now consistent.

### Board corrections — closed by asking, not building

- ✅ **AVATAR ROSTER TRANSPORT IS SHIPPED.** All 48 avatars upload, slot into the picker, and render everywhere including the wall. **It has been listed as Gate A's tail for weeks while it was already working.** ⚠️ **jAIne read the manifest's "READY, not yet uploaded" and reasoned from it instead of asking the person who uses the app daily.** **Gate A's tail is closed.**
- ✅ **THE ONBOARDING ADD-MEMBER AVATAR PICKER IS PRESENT AND SKIPPABLE.** Canon said a member added during onboarding could not choose an avatar, and inferred that every non-creator in every hold starts null by construction. **The truth is milder: the picker is offered and can be bypassed, leaving a themed empty circle rather than a broken render.** **The planned recon is dead; Scott answered it from the glass.** What remains is the empty-roster-seat decision, which is polish, not a defect.
- ✅ **`parent_self_redeem` RATIFIED AS INTENDED BEHAVIOR.** An adult redeeming their own embers is requester and approver in one call, inserting `status='approved'` outright. **This was never an open decision. It was intended behavior that had never been written down, which is a doc defect rather than a design question.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**THE PANEL, AS OF 08-03.** The fork decision moves four items from conditional to settled. **The panel needs one pass to reflect it.**

**Marked ignored, unconditionally as of 08-03:**
- ✅ **Adult PIN lock not tied to real permission checks.** The fork's reopen trigger is the only thing that revives it.
- ✅ **Redemption submitted on behalf of another member.** Same. ⚠️ **`wall_request_redemption` checks only that `_profile_id` belongs to the household and is active; it never compares against `auth.uid()`. That is now permanent design.**

**Should be marked ignored, action pending:**
- ⬜ **Kin read `adults_only` reward names and costs.**
- ⬜ **Kin read `parents_only` quest details.** **Both were left visible pending the fork. The fork is decided and they are unenforceable. Mark them ignored with the reasoning attached.**

**Marked ignored, reasoning already on record:**
- ✅ **"Public Can Execute SECURITY DEFINER Function"** — lint 0028/0029, permanently ignored.
- ✅ **"Any family member can forge shared activity-log entries"** — `actor_label`, and see the label decision below.
- ✅ **"System flags readable by any authenticated user"** — accepted with a named trigger.

**Proven stale and closed:**
- ✅ **"Adult PIN stored in plaintext in localStorage during signup" — THE SCANNER WAS WRONG AND CANON WAS RIGHT.** Only two localStorage keys exist in the entire application and neither is PIN-related.

**Accepted with a condition:**
- 🔵 **`public.system_flags`.** One global row, `('founder_gate_enabled', false)`. RLS on, `FOR SELECT TO authenticated USING (true)`, `anon` has no grant. ⚠️ **THE CONDITION: before the first non-public or non-boolean flag lands there, the read policy must be narrowed.** A Stripe mode flag, a rollout percentage or a kill switch would be world-readable to every account, silently. **This is a Phase 2 precondition.**

**Real, open:**
- ⬜ **🔴 THE SERVICE WORKER IS A SECURITY SURFACE.** It caches nothing, which is what makes it safe. ⚠️ **Any future caching work must NEVER cache a response carrying an Authorization header.** **A caching bug is the one defect on this board that cannot be fixed by pushing a fix.**
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Phase 2 privacy policy.**

**Fixed, historical:**
- ✅ **`quests.approved_by` adult validation — CLOSED AND VERIFIED ON THE GLASS 2026-08-02.** Mia completed, Scott approved, +10 landed, balance 111. ⚠️ **A stale "applied, not yet verified" warning sat in this doc's headline paragraph for a day after the SHIPPED section recorded the walk. Fixed 08-03. When a verify flips, sweep every place the old state is asserted, not just the one where the detail lives.**
- ✅ **`mark_first_run_complete` profile-scoping — CLOSED 2026-08-01.**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute · `anon` CRUD across all fifteen tables · Adult PIN plaintext in `localStorage`.

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**

---

## 🏷️ THE ACTIVITY LABEL — DECIDED 2026-08-03

**THE FEED STORES FACTS, NEVER DISPLAY TEXT.** `actor_label` splits into two columns.

**The evidence, from recon rather than from reasoning: the column conflates two facts at four of eight write sites.** Sites 2, 3, 5 and 6 (`vault.tsx:204`, `quest.$id.tsx:84`, `wall.tsx:166`, `Briefing.tsx:185`) write the **beneficiary** into `actor_label` while `actor_id` holds `auth.uid()`, which under own-session is always the owner. Sites 1, 4 and 8 write the actual actor. **Same column, two meanings, no way to tell them apart from the row.**

**THE SHAPE: `actor_id` for who clicked, a validated `subject_profile_id` for who gets credit, verb stays an enum, names resolve at render time.** That is option (b) from the parking lot, now confirmed by evidence.

**Rejected: keeping a stored display string.** The only genuine argument for it is that a string survives a deleted member and an ID does not. The answer is soft-delete or "a former Kin", and in a product whose tenant is a family, member churn is close to zero.

⚠️ **NOT DECIDED, AND IT IS SCOTT'S: whether the resolved name stays FROZEN.** The schema comment reads "frozen display name" and that is a deliberate choice. If Mia becomes Amelia, does history say Mia? **Frozen is defensible. It is a taste call and it is open.**

⚠️ **A jAINE ARGUMENT THAT WAS WRONG, RECORDED SO IT IS NOT REUSED.** jAIne argued that storing display text would leave a permanent Parent/Keeper seam across feed history. **False. `actor_label` stores member names, not role words.** The Keeper and Kin rename did not touch a single stored row. **The argument that survives is the two-meanings problem, and the recon proved it rather than the reasoning.**

**WHAT THIS UNBLOCKS: `logActivity` moving server-side is no longer blocked on a design question.** ✅ **And it is not a proposal: `admit_pending_member` and `deny_pending_member` already bypass the client helper entirely and INSERT from inside the SQL function, and they are the only two sites where the label is server-verified against the real identity.** **The pattern is shipped twice and working.** It is now blocked on credits and on a backfill design, which are scheduling problems.

⚠️ **CANON SAYS THREE VERB SWITCHES EXIST; THE RECON FOUND TWO** (`hearth-log.tsx` and `NotificationBell.tsx`). Either the recon missed one or canon has been carrying a third that is not there. **One curl to settle.**

---

## 🧰 THE TOOLCHAIN

- ⚠️ **BUN IS THE PACKAGE MANAGER. NEVER RUN npm OR yarn IN THIS REPO.** Must be a named line in every Code brief, exactly like `routeTree.gen.ts`. **bun 1.3.14 installed 2026-08-02; it had never been installed on this machine at all.** ✅ **Held across four more Code jobs on 08-03.**
- ⚠️ **EVERY PRIOR `npm audit` RESULT IN THIS PROJECT'S HISTORY IS VOID.**
- 🔵 **`bun audit` BASELINE, 2026-08-02: ten findings, all dev-tree, none shipping.** Re-run after any dependency change; ignore otherwise.
- 🔵 **THE 47 TYPECHECK ERRORS ARE REAL, PRE-EXISTING, ONE CLASS, AND DELIBERATELY NOT FIXED.** All 47 are TanStack Router reporting a missing `search` property. ⚠️ **Type layer only, zero runtime impact.** ✅ **Named as a baseline in every 08-03 brief and reported unchanged at 47 in all three commits. Naming the number turns a polluted output into a control.**
- ⚠️ **`routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** Name it in every Code brief. **Held across thirteen Code jobs without incident, because every brief named it.**
- ⚠️ **`query_quest.mjs` remains untracked in the working tree.** Named in every 08-03 brief and correctly left alone each time.

---

## ⬜ OPEN — the next work, in order

- ⬜ **🔴 GLASS-VERIFY THE THREE RENAME COMMITS.** The Vault as a Keeper and as a Kin, the Board, quest detail, and **the wall**, which took nine strings and is the only semi-public surface. **Free, and it is the difference between three commits and three shipped changes.**
- ⬜ **⚠️ `vault.tsx:820` TELLS A KEEPER TO ASK A KEEPER.** `KidCuratedVault`'s empty state is reused for both the Kin view and the Keeper's own "Your store" with no role prop, so it renders "No rewards yet. Ask a Keeper to stock the vault." **to the Keeper who would stock it.** **A real defect, deliberately left in place tonight because it is behavior rather than words.** Frontend-only, needs a prop or a conditional.
- ⬜ **THE WALL HAS NO AUDIENCE BADGE AT ALL.** `wall.tsx`'s `BountiesList` shows no restriction indicator for any bounty. The phone shows one. **Cross-surface asymmetry on the semi-public surface.** Scott's eye on whether the wall wants one.
- ⬜ **`onboarding.add-family.tsx` PipSpark body still says "this adult"** one line below a label that now says Keeper PIN. **A real recon hit that jAIne dropped from the brief; the agent correctly refused to fix what it was not asked to fix.** Rides along on the next job.
- ⬜ **🖊️ THE SCREEN COPY PASS.** **Slate ✅ · Ledger ✅ · Auth ✅ · Campaigns ✅ · Calendar ✅ · Briefing ✅ · everything else unreviewed.** ⚠️ **The rename touched auth, profile, Briefing, onboarding, PipHelp, PendingWaitingScreen, Vault, wall, Board, quest detail and setup-intent, but touching is not reviewing. Those screens have had a word swapped, not a voice read.**
- ⬜ **`logActivity` SERVER-SIDE.** Design question answered. **Blocked on credits and a backfill design for `subject_profile_id`, nothing else.**
- ⬜ **Vault favorites → real per-profile persistence.** Currently `localStorage`, so they do not follow a Kin to another device.
- ⬜ **The grant-revoke verification probe.** Drafted, deferred ten times. `SQLSTATE 42501` = FAIL, any application-level error = PASS. **Grant drift is the recurring security failure mode and nothing currently verifies the convention held.**
- ⬜ **Is `kids-only` a dead audience value?** See NOTED below. **One grouped read of `quests.audience` across all holds settles it. Should ride along on the next Code job.**
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward; verify before building.**
- ⬜ **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- ⬜ **The Briefing's FAB overlaps the Campaigns progress bar.** **Layout, Scott's eye.**
- ⬜ **`board.tsx:149`'s kicker may now stutter** against the `<h1>` below it. **Delete the kicker if it reads badly. Scott's eye.**
- ⬜ **THREE TEST OBJECTS ARE USER-VISIBLE:** `Testing redemption tracking`, `Testing retired`, `testing approve`. ⚠️ **`testing approve` minted 10 real embers to Mia and deleting the bounty does not unwind the balance.** **A standing question: do test mints get cleaned up, or accepted as the cost of verifying the economy on live data?**

---

## 🟠 NOTED — the `kids-only` audience tag may be dead

**Scott is ~92% confident no bounty in his hold has ever been set to `kids-only`, and the Slate shows no recurring ones.** The tag shipped tonight as "Kin only" because a badge is five lines and a value removal is a migration.

⚠️ **THE REASON IT IS UNUSED IS STRUCTURAL, NOT ACCIDENTAL, AND THE RENAME IS WHAT EXPOSED IT.** "Kids only" read as a rule a parent sets. **"Kin only" reads as a description of who happens to be around.** The tag was borrowing authority from the word "kid" and the rename stripped it out. **Keepers only excludes a Kin from something they should not have. Kin only excludes a Keeper from something they are perfectly capable of doing, in a household where the Keeper is the one who wants the work done.** There is no family scenario where that direction is useful.

**If a grouped read comes back empty across all thirteen holds, killing the value is a cheap migration on empty data and belongs in the Phase 2 pre-production sweep, taking the badge with it.** **Do not kill it on 92%.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE THREE RENAME COMMITS.** See OPEN, item one.
- 🟡 **🔴 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper.**
- 🟡 **🔴 THE MONTHLY ROLL BRANCH.** ⚠️ **August 1 is DONE and both legs passed. The next clean test is 2026-09-01. Do not ask Scott to read the monthly board.**
- 🟡 **`Testing retired` stays retired** once its successor's date arrives. Free, one look.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** A failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- 🟡 **`/create?recurring=true`** — the Slate's empty-state CTA is verified; the direct-URL half was not.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in: the approve half is routine; the WRONG-PIN half is still unexercised.**
- 🟡 **The ember progress trail** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Signup glass checks #2 and #3.**
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.

**Moved to Phase 2:** founder tier-tag verification (32 lock / 16 open). See below.

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Pure TypeScript, ten tests, zero Supabase imports, zero credits.**
- ⬜ **The engine cannot accept a pre-session constraint.** Scope with the rep-compensation job.
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.**
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **Two derivations of role.** ⚠️ **`useActiveMember().role` (client, switched profile) vs `has_role(auth.uid())` (server, always the owner). They disagree BY CONSTRUCTION whenever a Kin is active.** **The fork decision makes this permanent rather than pending.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** ⚠️ **Server-side only. Users do not set due dates and cannot see them. Recurrence anchors are a different thing and ARE visible as chips. Never ask Scott to set or check one.**
- ⬜ **Ghost successor cleanup.**
- ⬜ **`wall_request_redemption` is called from the Vault and its name lies.** Deliberate debt. **Rename when something else takes that function to Lovable.**
- ⬜ **The empty roster seat.** Present and skippable during add-member, leaving a themed empty circle. **Not a defect. Polish.** See parking lot.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Backup posture.** Critical path #1. **A distribution blocker until a human at Lovable names a retention window out loud.**
- ⬜ **Auth email deliverability.** Deferred by choice pending DNS spend. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Grant-revoke verification probe job.**
- ✅ **`sandbox_exec` — ANSWERED AND CLOSED. Do not revoke.**
- ✅ **Service worker — SHIPPED for installability only. The offline shell is DEPRIORITIZED, deliberately.**

## ⬜ OUTSTANDING — ship-blocking debt

- ✅ **Avatar roster transport — SHIPPED.** Upload, picker slotting, render, all live.
- ⬜ **Vault favorites → real per-profile persistence.**
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The screen copy pass (running)** · **`og:title` on 2 routes but not 31** · **The stacked-Pip-voice line on the first setup screen** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **The Briefing FAB overlapping the Campaigns bar** · **`board.tsx:149`'s kicker stutter** · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The offline shell** · **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs, monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **empty-roster-seat** · **an application-level export routine** · **the avatar free/paid split (moved to Phase 2)** · Favorites on the wall · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model.

**Left the parking lot tonight:** own-session vs per-member-auth (DECIDED), role-label retirement (DONE, shipped as Keeper and Kin), `actor_label`'s security half (settled by the fork).

---

## 🔴 WALK-UP TRUST BOUNDARY — NOW PERMANENT

Switching into a PIN-less Keeper profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary. **Physical possession of an unlocked Keeper session equals Keeper authority.** In the shared-session model a device-Kin holds the owner's ambient JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`.

**AS OF 2026-08-03 THIS IS NOT AN ACCEPTED WORKAROUND. IT IS THE DECIDED ARCHITECTURE**, with flat or peer holds as the single named reopen trigger.

⚠️ **`useActiveMember()` reads the switched-to profile's own `role` column client-side; every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a Kin is acting; the database always thinks the owner is.** **Three RPCs take a validated actor id precisely to bridge it** — `approve_redemption`, `wall_request_redemption`, `mark_first_run_complete` — and a fourth site validates in a trigger because the path has no RPC. **The principle is "the database validates the actor," not "an RPC takes an optional id."**

**The PIN is the human boundary and it works: it stops a Kin from walking into a Keeper profile, which is the thing that actually happens in a house. What it does not do is stop devtools, because the PIN gates the client and the database never sees it.** Own-session accepts that the machine boundary is unenforceable and keeps the human one.

---

## 🔵 THE BUILD MODEL — holding

- **ASK THE USER BEFORE BELIEVING THE DOC. (NEW — 2026-08-03.)** Avatar transport sat on the critical path while all 48 avatars were live, because jAIne read the manifest's "READY, not yet uploaded" and reasoned from it. **The manifest is a claim about a file. Scott is the one who opens the app.** **When canon describes a shipped state and Scott is available, ask.**
- **A RENAME IS AN INVENTORY PROBLEM, NOT A STRING PROBLEM. (NEW — 2026-08-03.)** Two jobs shipped against a recon that searched for the literal words "Parent" and "Kid". **Both misses were the same concept in other words: a roster badge reading "ADULT" and a card line reading "Available to anyone".** **The correct recon works backwards from the DATA: find every read of the field, then trace which reads reach the eyes.** That version found 35 role surfaces and 5 audience surfaces, including three live defects.
- **THE MAP IS FOR LABELS, NOT FOR PROSE. (NEW — 2026-08-03.)** `ROLE_LABEL` earns its keep where a bare label renders from a stored value. **It cannot help with "Ask a Keeper to stock the vault," because that is prose with the word embedded and no role value is read.** **Forcing interpolation into twenty sentences is worse than the duplication.** Say so explicitly in the brief or an agent will over-extract.
- **A DECISION CAN DISSOLVE AN ITEM INSTEAD OF SCOPING IT. (NEW — 2026-08-03.)** The fork blocked four items for weeks. Deciding it produced zero builds and four reclassifications. **The board looked thin because one unknown was making four knowns unscopable.**
- **WHEN A VERIFY FLIPS, SWEEP EVERY PLACE THE OLD STATE IS ASSERTED. (NEW — 2026-08-03.)** `approved_by` was recorded as verified in the SHIPPED section and still warned as unverified in the headline paragraph of the same document. **Full rip-and-replace exists to prevent exactly this and did not, because the doc was regenerated as sections rather than as one document.**
- **THE PATTERN IS THE PRINCIPLE, NOT THE IMPLEMENTATION.**
- **THE BOARD CANNOT DISTINGUISH "NEVER DONE" FROM "DONE DAILY."** ⚠️ **Five instances in two sessions. jAIne asks before carrying an item forward.**
- **A TOOL NOBODY VERIFIED WAS INSTALLED HAS BEEN SILENTLY SUBSTITUTED FOR MONTHS.** **Verify the tool, not the convention that says which tool.**
- **A SCANNER'S MEMORY IS A CLAIM, AND A STALE ONE POISONS THE PANEL.**
- **A DESIGN RULE LIVING IN A SOURCE COMMENT DOES NOT SURVIVE THE NEXT REWRITE OF THAT FILE.**
- **A STOP-CLAUSE IN A BRIEF IS WORTH MORE THAN A CORRECT INSTRUCTION.** ✅ **Fired on 08-03 and was right: the audience field has THREE values, not two, and an entire Part 2 was built on a binary that does not exist.** **Three fires, three correct, across two sessions.**
- **NAME THE KNOWN-ERROR BASELINE IN THE BRIEF. (NEW — 2026-08-03.)** "47 pre-existing TanStack errors, report only if the count moves" turned a polluted typecheck into a control that three jobs reported cleanly against.
- **AN INVARIANT MUST BE AN EXPLICIT LINE IN THE BRIEF, NOT INFERRED FROM PROSE.** ✅ **"IDENTIFIERS DO NOT CHANGE" as its own block held across three commits touching 27 files.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.** `git show --stat` in every brief.
- **A Code job isn't done until the artifact is observable from outside the agent.** `git push` and `git rev-parse origin/main` in every brief.
- **A PLAN ITERATION COSTS A CREDIT.** **Review a plan in ONE pass.**
- **JAINE'S PUNCTUATION REACHES THE GLASS THE SAME WAY HER RATIONALE DOES.** ⚠️ **No em dashes in user-facing copy or in briefs handed downstream.**
- **READ THE WHOLE MESSAGE BEFORE ANSWERING IT.** · **ANSWER THE QUESTION ACTUALLY ASKED, AT THE REGISTER IT WAS ASKED.** · **ASK ONE QUESTION BEFORE WRITING A FINDING.** · **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH.** ⚠️ **And one doc can contradict itself. The one describing SHIPPED BEHAVIOR wins.**
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.**
- **BRIEF THE RECON TO DISPROVE.**
- **A SUBTITLE EXPLAINING SOMETHING VISIBLE IS BRIEF TEXT THAT ESCAPED ONTO THE GLASS.** **Default is deletion.** ⚠️ **Unless the surface is empty.** ✅ **And vertical height on a scrolling phone board is its own argument, independent of the copy rule.**
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED.**
- **FIX THE CONTAINER, NOT THE CONTENT.** · **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.** *"Vaccuum Downstairs" has survived six sweeps and stays misspelled.*
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** ⚠️ **The defining bug class of the codebase.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **Fetch the canon before producing anything.** ⚠️ **And verify the file, not the tracking item that describes it.**
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis, synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** · **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-08-03** — the decision session. The own-session fork decided and LOCKED, closing four items by reclassification. Keeper and Kin locked and shipped across three commits. The activity label resolved to a two-column split. `parent_self_redeem` ratified. Avatar transport and the onboarding avatar picker corrected on the board. Three live defects found and two fixed. Zero credits, four Code jobs. `9c83fc4` → `e8223f4` → `f9f731e`.
- **2026-08-02** — the assessment session. `quests.approved_by` validated server-side via trigger and verified on the glass; `SlateEmpty()` rewrite published; vitest typecheck cleared; bun installed and the toolchain corrected; the security panel triaged to zero mysteries; Stripe reframed as Phase 2. ~1.9 credits. `9963e12` plus one migration.
- **2026-08-01 (late)** — the free session. Service worker shipped for installability; master-spec fold; page titles; Slate collapsed-group label. Zero credits. `e813dca` → `b2efd8f`.
- **2026-08-01 (early)** — the marker session. First-run marker read/write fixed via a shared resolver; copy pass batch two plus a 125-character em-dash sweep. **August 1 roll-forward passed both legs.** ~2.8 credits. `eb93e73` → `fd1d6a8`.
- **2026-07-31** — the redemption-path session. Vault kid-redemption fixed by routing to the existing wall RPC; wall approvals now log; the Slate copy pass. Zero credits. `e186ff0` → `1ec7af2`.
- **2026-07-30 (night)** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. Five credits.
- **2026-07-30 (late)** — QA/design session. Bounty supersedes Quest; the roll-forward rule.
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

# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-08-02** — *the assessment session.* **~1.9 Lovable credits. Four Code jobs, one migration, one roadmap reframe, one security panel triaged to zero mysteries.**

**The headline: the roadmap was reframed, and the board is thin because the big things were never decomposed, not because they are small.** Stripe and the Founding Guildhall are now **Phase 2**, explicitly after a complete and ready Emberhold. **"Ready" is Gate B's exit criterion and nothing more.** What is left before it is roughly twenty items, six of which are questions rather than builds, and exactly one of which is a genuine unknown: **the own-session versus per-member-auth fork**, which sits upstream of four other items and cannot be scoped until it is decided.

**`quests.approved_by` IS VALIDATED SERVER-SIDE FOR THE FIRST TIME, AND THE SHAPE IS A TRIGGER RATHER THAN AN RPC.** The stop-clause caught it: there is no quest-approval RPC at all. Five client surfaces write `approved_by` directly with a bare `UPDATE`. **`enforce_quest_family_refs` now also requires that a non-null `approved_by` names an active adult in the same household.** ⚠️ **Applied, not yet verified on the glass.**

⚠️ **88 EXISTING APPROVALS, EVERY ONE NAMING AN ACTIVE ADULT.** The hole was open for the entire life of the feature and nobody ever fell in it. **Nothing legitimate was broken by closing it, and there is no bad historical row to go looking for.**

**BUN WAS NEVER INSTALLED ON THIS MACHINE.** Every Code job this project has ever run silently used npm, against a `package-lock.json` that is not this project's lockfile. **Which means every `npm audit` result ever recorded here was a statement about the wrong dependency tree.** bun 1.3.14 is now installed, the tree is restored, and the stale lockfile is deleted.

**THE FIRST HONEST DEPENDENCY SCAN RETURNED TEN FINDINGS AND ALL TEN ARE DEV-TREE.** Every one traces through eslint, vite, babel, tsx, or typescript-eslint. **None reach a browser.** The esbuild advisory literally reads "when running the development server on Windows," which is an attack on Scott's laptop rather than on a household.

**THE LOVABLE SECURITY PANEL NOW CONTAINS ZERO MYSTERIES.** Seven warnings became three real ones. The PIN finding was proven stale, `system_flags` was identified and accepted with a named trigger, `sandbox_exec` was answered by Lovable, and three known-accepted items were marked ignored so the panel stops training Scott to skim it.

Last session (prior): **2026-08-01 (late)** — the free session. Zero credits, five Code jobs, service worker shipped.
Last session (prior): **2026-07-31 (night) → 2026-08-01 (early)** — the marker session. ~2.8 credits.
Last session (prior): **2026-07-31 (day)** — the redemption-path session.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## 🧭 THE REFRAME — WHAT "READY" MEANS NOW

**Phase 2 is Stripe, the Founding Guildhall, refunds, tax, COPPA, and the privacy policy.** It is real, it is next, and it is explicitly **not** part of getting Emberhold finished. It was one row on the critical path pretending to be one task, and it is at least fifteen.

**Phase 1 is "a complete and ready Emberhold," and its definition is Gate B's exit criterion, verbatim:** a stranger onboards unassisted, tenant isolation holds, the first email they receive is from Emberhold and reaches the inbox, and offline shows a themed shell.

**Against that bar the remaining work is not code.** Security is done and provably so. The feature loop is done. Adult onboarding is done. What is left is four Gate B items, a decision, some mechanism debt, the avatar transport, and a copy pass.

⚠️ **THE BOARD CANNOT TELL THE DIFFERENCE BETWEEN "NEVER DONE" AND "DONE SO OFTEN NOBODY NARRATES IT."** Three items sat as pending verifies tonight that are in fact routine daily life. **This is the rumor problem running in the other direction, and it is more expensive, because it costs attention every time the board is read.** The fix is free: jAIne asks before carrying an item forward, and Scott says normal, never happened, or don't care.

---

## Where the platform is

**Structurally complete, published, installable on iOS, with a working activation path and a working spend path for every role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, tenant isolation verified under live attack, clean function and table grant surfaces, the Slate, the Ledger, a rollover engine verified on a real month boundary, a registered service worker, and **as of tonight a server-validated quest approver.**

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> ⚠️ **RESIDUAL, UNCHANGED:** roughly 400 lines of master-spec, Part II (Forge), the onboarding sections and the schema detail, remain unread since the 07-29/07-30 folds. **A full cold read is worth doing eventually; it is not urgent.**

---

## 🔴 THE CRITICAL PATH — PHASE 1 ONLY

| # | Item | Blocks |
|---|---|---|
| **1** | **⚠️ THE OWN-SESSION VS PER-MEMBER-AUTH FORK.** Parked. **Four open items wait on it and cannot be scoped until it is decided.** This is a decision session, not a build session. **Do not brief it as a build.** | Both audience reads. Two ignored findings. Any real estimate of Phase 1. |
| **2** | **🟡 THE ANDROID INSTALL PROMPT.** Scott's, in progress on his own schedule. **Chrome proper, not Fully Kiosk.** | Gate B. The wall device. |
| **3** | **⬜ BACKUP POSTURE.** ⚠️ **Lovable could not confirm PITR, backup schedule, or retention.** Self-service export exists at **Cloud → Advanced settings → Export data**. **Live user data, no confirmed recoverable backup.** | Gate B. Distribution. |
| **4** | **⬜ AUTH EMAIL DELIVERABILITY.** One of six providers still spams. **Deferred by choice: Scott is not spending on DNS until the end.** Tracked, not forgotten. | Gate B. Every stranger signup. |
| **5** | **⬜ AVATAR ROSTER TRANSPORT.** Storage upload → picker slotting → free/paid split. | Gate A's tail. |

**Phase 2 (Stripe, Guildhall, COPPA, privacy policy, refunds, tax) is deliberately absent from this table.**

---

## ✅ SHIPPED — 2026-08-02

### `quests.approved_by` server-side validation *(migration, ~1.9 credits)*

- ✅ **`enforce_quest_family_refs` extended.** A non-null `approved_by` must now name a profile in the same household with an adult role and active status. **The existing same-family check is intact, not replaced.**
- ✅ **Trigger, not RPC, and that is the correct shape rather than the cheap one.** ⚠️ **THE STOP-CLAUSE FIRED AND WAS RIGHT: there is no quest-approval RPC to add an optional actor parameter to.** Five surfaces write `approved_by` directly: `quest.$id.tsx` (two sites), `wall.tsx`, `slate.tsx`, `quest-log.tsx`, `Briefing.tsx`. **The pattern being copied is "the database validates the actor," not "an RPC takes an optional id." A trigger validates on arrival and covers all five at once.**
- ✅ **No `auth.uid()` comparison anywhere in the change.** A switched-to adult profile is a valid approver and stays one. **The walk-up boundary is untouched.**
- ✅ **NULL `approved_by` stays legal.** Creation, claiming and every non-approval update are unaffected.
- ✅ **Zero client files modified.** All five call sites keep working unchanged.
- ⚠️ **88 pre-existing approvals all name active adults.** Nothing legitimate breaks.
- ✅ **VERIFIED ON THE GLASS 2026-08-02.** Mia completed `testing approve`, Scott approved from his own profile, no errors anywhere in the walk. **+10 landed on Mia, credited to the correct bounty, balance 111.** Approval commits, the mint routes to the completing member, attribution is correct. **Shipped, not applied.**

### The `SlateEmpty()` rewrite *(published and live)*

- ✅ **Replaced with "Repeating bounties live here. The trash, the dishes, Monday laundry."** Mascot and CTA untouched. Three sentences to one.
- ✅ **`laundry,it` is gone**, killed by the string that contained it. The em-dash sweep's only shipped casualty is closed.

### The vitest typecheck error *(`9963e12`)*

- ✅ **`"vitest"` added to the `types` array in `tsconfig.json`.** One line, `+1/-1`, verified by `git show --stat`. **Eleven consecutive Code jobs stop reporting it.**
- ⚠️ **The job that fixed it ran `npm install` in a bun repo**, which created a `package-lock.json` and resolved `@tanstack/react-router` to 1.170.17 against bun.lock's 1.170.16. **Both artifacts have since been cleaned.**

### Items closed by asking rather than building

- ✅ **KID REDEMPTION IS ROUTINE, NOT PENDING.** Scott's kid presses Redeem regularly, with PIN and adult approval, and it works. **That is a completed end-to-end loop verified on the glass repeatedly in production.** It has been carried as unproven since 07-31.
- ✅ **WALL ADULT-VERIFIED TURN-IN IS ROUTINE.**
- ✅ **THE RECURRENCE CHIP AGREES ACROSS SLATE, BOARD AND CREATE/EDIT.**
- ✅ **`sandbox_exec` IS ANSWERED AND CLOSED.** Platform-managed Lovable Cloud execution role for the agent sandbox, migration runner and schema introspection. **`postgres` is a member with ADMIN OPTION and inherits from it.** ⚠️ **Do not revoke.** Revoking would break migrations, agent introspection, and the `postgres` → `sandbox_exec` privilege chain. **Narrowing, if ever wanted, is a Lovable support conversation, never a manual revoke.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**THE LOVABLE PANEL, TRIAGED 2026-08-02.** Seven warnings became three real ones.

**Marked ignored, with reasoning already on record:**
- ✅ **"Public Can Execute SECURITY DEFINER Function"** — lint 0028/0029, permanently ignored. `founder_gate_enabled()` and `my_household_is_founder()` were tightened to `authenticated` only in the 07-20 migration. **The scanner is objecting to SECURITY DEFINER existing, which is its purpose.**
- ✅ **"Any family member can forge shared activity-log entries"** — this is `actor_label`, downgraded and re-scoped as design weeks ago. Intra-household under the walk-up boundary.
- ✅ **"System flags readable by any authenticated user"** — **IDENTIFIED AND ACCEPTED WITH A NAMED TRIGGER.** See below.

**Proven stale and closed:**
- ✅ **"Adult PIN stored in plaintext in localStorage during signup" — THE SCANNER WAS WRONG AND CANON WAS RIGHT.** Recon found **only two localStorage keys in the entire application**: `emberhold:active-member` and `emberhold:lastSection:{listId}`. Neither is PIN-related. The PIN lives in React state during form entry and goes only to `set_profile_pin` over HTTPS. **The scanner is holding a memory of the pre-07-26 signup path.** ⚠️ **A stale entry in a scanner's memory is worse than a wrong finding: it dilutes the panel and trains the reader to skim, which is how a real one gets missed.**

**Identified tonight:**
- 🔵 **`public.system_flags` — ACCEPTED, WITH A CONDITION.** One table, three columns (`key`/`value`/`updated_at`), holding a single global row: `('founder_gate_enabled', false)`. RLS enabled, policy `FOR SELECT TO authenticated USING (true)`. **`anon` has no grant and no policy: it cannot read the table at all.** No `family_id` column, no per-tenant data, no cross-household leakage. **Every authenticated user reads the same `false`.** ⚠️ **THE CONDITION: the table is a bare key/value store with nothing constraining what goes into it. Before the first non-public or non-boolean flag lands there, the read policy must be narrowed.** A Stripe mode flag, a rollout percentage or a kill switch inserted into this table would be world-readable to every account, silently. **This is a Gate C precondition, written down so the next agent does not cheerfully insert one.**

**Real, open:**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class, fix together, both wait on the own-session fork. The top open security items.** **Deliberately left visible on the Lovable panel rather than ignored.**
- ⬜ **🔴 THE SERVICE WORKER IS A SECURITY SURFACE.** It caches nothing, which is what makes it safe. ⚠️ **Any future caching work must NEVER cache a response carrying an Authorization header.** That is tenant isolation reintroduced at the cache layer, on a boundary verified under live attack. **And a caching bug is the one defect on this board that cannot be fixed by pushing a fix.**
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Phase 2 privacy policy.**

**Fixed, historical:**
- ✅ **`quests.approved_by` adult validation — CLOSED 2026-08-02** (pending glass).
- ✅ **`mark_first_run_complete` profile-scoping — CLOSED 2026-08-01.**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage`.

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** ⚠️ **`wall_request_redemption` checks only that `_profile_id` belongs to the household and is active; it never compares against `auth.uid()`.**

---

## 🧰 THE TOOLCHAIN — CORRECTED 2026-08-02

- ⚠️ **BUN IS THE PACKAGE MANAGER. NEVER RUN npm OR yarn IN THIS REPO.** This must be a named line in every Code brief, exactly like `routeTree.gen.ts`. **bun 1.3.14 installed 2026-08-02; it had never been installed on this machine at all.**
- ⚠️ **EVERY PRIOR `npm audit` RESULT IN THIS PROJECT'S HISTORY IS VOID.** It read `package-lock.json`, which is not this project's lockfile. **"0 vulnerabilities" was never a statement about the shipped dependency tree.**
- ✅ **`package-lock.json` IS DELETED** and the tree restored via `bun install` at 1.170.16, matching `bun.lock`.
- 🔵 **`bun audit` BASELINE, 2026-08-02: ten findings, six high, two moderate, two low. ALL DEV-TREE.** `@babel/core`, `brace-expansion`, `postcss`, `esbuild`, `js-yaml`, every one reached through eslint, vite, babel, tsx or typescript-eslint. **None ship to a browser.** ⚠️ **Ten sounds worse than Lovable's two and is arguably less urgent: Lovable's panel scans the 55-package runtime tree, `bun audit` walks the full dev tree. Different sets, not a contradiction.** **Re-run after any dependency change; ignore otherwise.**
- 🔵 **THE 47 TYPECHECK ERRORS ARE REAL, PRE-EXISTING, AND ONE CLASS.** Hypothesis that they were an npm-resolution artifact is **DISPROVED**: identical count under npm's 1.170.17 and bun's 1.170.16. **All 47 are TanStack Router reporting a missing `search` property on navigate, redirect and Link calls; 36 match that string literally.** ⚠️ **Type layer only. Zero runtime impact. Every affected path works on the glass daily.** **Deliberately not fixed: 36 call sites across every route in the app, for zero user-visible change, is Phase 2 cleanup at best.** Revisit only if a router upgrade forces it.
- ⚠️ **`routeTree.gen.ts` DRIFT IS CONFIRMED LIVE.** Name it in every Code brief. **Held across nine Code jobs without incident, because every brief named it.**
- ⚠️ **`query_quest.mjs` remains untracked in the working tree.**

---

## ⬜ OPEN — the next work, in order

- ⬜ **🖊️ THE SCREEN COPY PASS — RUNNING.** **Slate ✅ · Ledger ✅ · Auth ✅ · Campaigns ✅ · Calendar ✅ · Briefing ✅ · everything else unreviewed.**
- ⬜ **⚠️ THE ONBOARDING ADD-MEMBER AVATAR PICKER — UNCONFIRMED, AND BIGGER THAN THE OLD ITEM.** The old tracking item read "avatar render fallback, check the wall," which invites Scott to look at his own wall and find nothing, because every member of his hold picked a face during first run. **The real report: a member added during onboarding could not choose an avatar.** If that holds, **every non-creator member in every hold starts with a null avatar by construction.** Not an edge case, the default path. **Needs one free recon: does the onboarding add-member flow offer a picker, and what renders when `avatar` is null.**
- ⬜ **`logActivity` IS A CLIENT-SIDE BOLT-ON.** Every call site can forget, and one did for the entire life of the wall's approve path. **The mechanism fix is the definer RPC writing its own log row.** ⚠️ **BLOCKED ON A DESIGN QUESTION: does the display label belong in the DB at all?** 08-01 weakened the case that it does. **Do not write the migration before answering it.**
- ⬜ **`parent_self_redeem` INSERTS `status='approved'` OUTRIGHT.** An adult redeeming their own embers skips the approval gate. **By design per the code, never written down.**
- ⬜ **Vault favorites → real per-profile persistence.** Currently `localStorage`, so they do not follow a kid to another device.
- ⬜ **The grant-revoke verification probe.** Drafted, deferred nine times. `SQLSTATE 42501` = FAIL, any application-level error = PASS. **Grant drift is the recurring security failure mode and nothing currently verifies the convention held.**
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward; verify before building.**
- ⬜ **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- ⬜ **The Briefing's FAB overlaps the Campaigns progress bar.** **Layout, Scott's eye.**
- ⬜ **THREE TEST OBJECTS ARE USER-VISIBLE: `Testing redemption tracking`, `Testing retired`, and `testing approve`.** Prod test-object cleanup. ⚠️ **`Testing retired` sits in the Retired section and canon records no un-retire affordance.** ⚠️ **`testing approve` minted 10 real embers to Mia during the 08-02 verification walk, and deleting the bounty does not unwind the balance.** **A standing question, not urgent: do test mints get cleaned up, or are they accepted as the cost of verifying the economy on live data?**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE ANDROID INSTALL PROMPT.** Scott's, on his own schedule. **Chrome proper.**
- 🟡 **🔴 THE MONTHLY ROLL BRANCH.** ⚠️ **August 1 is DONE and both legs passed. The next clean test is 2026-09-01. Do not ask Scott to read the monthly board.**
- 🔵 **THE KID JOINER WALK — DOWNGRADED 2026-08-02, SCOTT'S CALL.** It is adult-supervised at the exact moment it runs, and an adult over a kid's shoulder is a live debugger with a phone. **The failure mode being protected against was silent failure with nobody watching, and this is not that.** **Will surface itself. Stop carrying it as a blocker.**
- 🟡 **`Testing retired` stays retired** once its successor's date arrives. Free, one look.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** A failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- 🟡 **`/create?recurring=true`** — the Slate's empty-state CTA is verified; the direct-URL half was not.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in: the approve half is routine; the WRONG-PIN half is still unexercised.**
- 🟡 **The ember progress trail** · **Founder tier-tag verification (32 lock / 16 open)** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Signup glass checks #2 and #3.**
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.

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

- ⬜ **Two derivations of role.** ⚠️ **`useActiveMember().role` (client, switched profile) vs `has_role(auth.uid())` (server, always the owner). They disagree BY CONSTRUCTION whenever a kid is active.**
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** ⚠️ **Server-side only. Users do not set due dates and cannot see them. Recurrence anchors are a different thing and ARE visible as chips. Never ask Scott to set or check one.**
- ⬜ **Ghost successor cleanup.**
- ⬜ **`wall_request_redemption` is called from the Vault and its name lies.** Deliberate debt. **Rename when something else takes that function to Lovable.**

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Backup posture.** ⚠️ **Lovable could not confirm PITR, schedule or retention from the project side.** Self-service export at **Cloud → Advanced settings → Export data**. **A distribution blocker until a human at Lovable names a retention window out loud.**
- ⬜ **Auth email deliverability.** Deferred by choice pending DNS spend. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ✅ **`sandbox_exec` — ANSWERED AND CLOSED. Do not revoke.**
- ✅ **Service worker — SHIPPED for installability only. The offline shell is DEPRIORITIZED, deliberately.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar roster transport** — storage upload → picker slotting → free/paid split.
- ⬜ **Vault favorites → real per-profile persistence.**
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The screen copy pass (running)** · **`og:title` on 2 routes but not 31** · **The stacked-Pip-voice line on the first setup screen** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **The Briefing FAB overlapping the Campaigns bar** · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The offline shell** · **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs, monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat** · **an application-level export routine** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked and it is now critical path #1.

⚠️ **`useActiveMember()` reads the switched-to profile's own `role` column client-side; every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is.** **Three RPCs take a validated actor id precisely to bridge it** — `approve_redemption`, `wall_request_redemption`, `mark_first_run_complete`. ⚠️ **Tonight's `approved_by` fix is a FOURTH instance of the principle but NOT a fourth RPC: it validates in a trigger, because the path has no RPC. The principle is "the database validates the actor," not "an RPC takes an optional id."**

---

## 🔵 THE BUILD MODEL — holding

- **THE PATTERN IS THE PRINCIPLE, NOT THE IMPLEMENTATION. (NEW — 2026-08-02.)** A brief said "copy `approve_redemption`." The path had no RPC to copy it into. **The thing worth copying was "the database validates the actor rather than trusting the client," and a trigger does that at all five call sites at once.** Rewriting five surfaces to reach the same security outcome would have been the faithful pattern match and the wrong trade, especially with the Briefing's credit-reassignment and the wall's PIN flow inside the blast radius.
- **THE BOARD CANNOT DISTINGUISH "NEVER DONE" FROM "DONE DAILY." (NEW — 2026-08-02.)** Three pending verifies were routine life. **This is the rumor problem inverted, and it is worse: rumors waste a session, stale verifies waste attention every time the board is read.** **jAIne asks before carrying an item forward.**
- **A TOOL NOBODY VERIFIED WAS INSTALLED HAS BEEN SILENTLY SUBSTITUTED FOR MONTHS. (NEW — 2026-08-02.)** Every Code job used npm on a bun repo because bun was never installed and nobody checked. **Every audit result was about the wrong tree.** **Verify the tool, not the convention that says which tool.**
- **A SCANNER'S MEMORY IS A CLAIM, AND A STALE ONE POISONS THE PANEL. (NEW — 2026-08-02.)** The PIN finding described a signup path that was rebuilt on 07-26. **A panel with one ghost and four known-accepted items trains the reader to skim, which is how a real finding gets missed.** **Triage the panel to zero mysteries, then keep it there.**
- **A DESIGN RULE LIVING IN A SOURCE COMMENT DOES NOT SURVIVE THE NEXT REWRITE OF THAT FILE. (NEW — 2026-08-02.)** `auth.tsx` lines 22 to 24 carry the no-client-side-stash rule as prose in a comment. **It is now in `decisions.md`, where it belongs.**
- **A REPUBLISH IS CHEAPER THAN A THEORY.** When a file is in the repo, in the deploy, and absent from the origin, publish again before building a model of why.
- **A TRACKING ITEM WITH NO EVIDENCE AND NO DATE IS A RUMOR.**
- **A RECON ANSWERS THE QUESTION IT WAS BRIEFED WITH.** Do not generalize a finding to the container the question lived in.
- **A STOP-CLAUSE IN A BRIEF IS WORTH MORE THAN A CORRECT INSTRUCTION.** ✅ **Fired twice tonight and was right both times:** bun's absent PATH, and the missing quest-approval RPC. **Cheap to write, converts a wrong brief into a finding.**
- **THE FIRST VERSION OF A GUARD CAN CONTAIN THE RACE IT WAS MEANT TO PREVENT.**
- **A PLAN ITERATION COSTS A CREDIT.** **Review a plan in ONE pass.**
- **AN INVARIANT MUST BE AN EXPLICIT LINE IN THE BRIEF, NOT INFERRED FROM PROSE.**
- **JAINE'S PUNCTUATION REACHES THE GLASS THE SAME WAY HER RATIONALE DOES.** ⚠️ **No em dashes in user-facing copy or in briefs handed downstream.**
- **READ THE WHOLE MESSAGE BEFORE ANSWERING IT.**
- **ANSWER THE QUESTION ACTUALLY ASKED, AT THE REGISTER IT WAS ASKED.**
- **ASK ONE QUESTION BEFORE WRITING A FINDING.**
- **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH.** **The one describing SHIPPED BEHAVIOR wins.**
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.**
- **BRIEF THE RECON TO DISPROVE.** ✅ **Worked three times tonight: the npm hypothesis died, the PIN finding died, `system_flags` came back harmless.**
- **A SUBTITLE EXPLAINING SOMETHING VISIBLE IS BRIEF TEXT THAT ESCAPED ONTO THE GLASS.** **Default is deletion.** ⚠️ **Unless the surface is empty.**
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED.**
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS.**
- **FIX THE CONTAINER, NOT THE CONTENT.** · **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.** *"Vaccuum Downstairs" has survived five sweeps and stays misspelled.*
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** ⚠️ **The defining bug class of the codebase.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.** ⚠️ **Reinforced tonight: a job reported "only tsconfig changed" and it took `git show --stat` to confirm it.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **Fetch the canon before producing anything.** ⚠️ **And verify the file, not the tracking item that describes it.**
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis, synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** · **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-08-02** — the assessment session. `quests.approved_by` validated server-side via trigger; `SlateEmpty()` rewrite published; vitest typecheck cleared; bun installed and the toolchain corrected; the Lovable security panel triaged to zero mysteries; Stripe reframed as Phase 2. ~1.9 credits, four Code jobs. `9963e12` plus one migration.
- **2026-08-01 (late)** — the free session. Service worker shipped for installability; master-spec fold done from a read; page titles to middot; Slate collapsed-group label; `member_admitted`/`member_denied` rendered. Three carried items closed by looking. Zero credits. `e813dca` → `b2efd8f`.
- **2026-08-01 (early)** — the marker session. First-run marker read/write fixed via a shared resolver; `verbLabel` scoped and closed without a fix; copy pass batch two plus a 125-character em-dash sweep. **August 1 roll-forward passed both legs.** ~2.8 credits. `eb93e73` → `fd1d6a8`.
- **2026-07-31** — the redemption-path session. Vault kid-redemption fixed by routing to the existing wall RPC; wall approvals now log; the Slate copy pass. Four Code jobs, zero credits. `e186ff0` → `1ec7af2`.
- **2026-07-30 (night)** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. Five credits.
- **2026-07-30 (late)** — QA/design session. Bounty supersedes Quest; the Slate and the Ledger; the roll-forward rule.
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

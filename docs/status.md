# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-30** — *the redemption `decided_by` fix.* A design + Lovable + Code session on 5 credits, closed with ~1.2 remaining. **CRITICAL PATH #1 is CLOSED and verified on live data.** `approve_redemption` and `deny_redemption` now accept a validated optional approver id; the wall passes the PIN-verified adult it was already computing and silently discarding.

**The item was built for a different reason than the one the docs were shouting.** Scott challenged the priority directly — *where does anyone actually see who approved this?* — and recon confirmed the answer: **nowhere.** `vault.tsx` selects `decided_by` into every row and no component renders or branches on it. The 🔴 had been carried since 07-29 on the strength of a breached-deadline framing and a PIN-screen promise no user can falsify. **It got built because it turned out to cost ~1 credit and zero taps, not because it was urgent.** Second instance of *severity in a doc outliving the evidence for it*, and the first where the challenge came from Scott rather than from recon.

**The recon was briefed to disprove and two of five disproofs landed — both against jAIne's expectations.**
- **D4 FAILED, which inverts the spec's own prescription.** `master-spec.md` said quest approval's behavior was correct and redemption should move to match it. It isn't. `quests.approved_by` is validated for same-family membership and for the *session owner* holding the parent role — **never that the written value names an adult at all.** A client can write a kid's profile id into `approved_by` and both triggers pass. **Redemption now validates strictly more than the thing we were told to copy.**
- **D5 FAILED, and jAIne had put it at better than even.** The redemption identity comes from `verify_profile_pin` against the adults list; the first-run marker's comes from `getActiveMemberIdSync()`. No shared helper, hook, or RPC. **Critical path #1 and #2 share a pattern, not a dependency** — #2 was left completely untouched, as scoped.

**Lovable's role-source answer was better than the one jAIne would have written.** The brief flagged profile-id-vs-user-id as a pedantry check; it is a live false negative. `has_role()` reads `user_roles.user_id`, which only exists for profiles backed by an `auth.users` account — so it would have **rejected the exact co-parent the feature exists to record.** `profiles.role` + `status = 'active'` is authoritative for an arbitrary profile id, and is trigger-protected by `enforce_profile_role_change`. The session-owner authority gate still uses `has_role(auth.uid(), 'parent')`, unchanged.

**Verified on live data, because there is no glass.** Nothing renders `decided_by` — which is what made the fix cheap and what makes "verified on the glass" structurally unavailable. A wall approval under the co-parent's PIN produced the **first row in the table's history where `decided_by` is not the account holder**, and the validation branch passed without raising, meaning the server independently confirmed the id was an active adult in-family.

Last session (prior): **2026-07-29 (late)** — the master-spec fold + the first-run completion marker. *(That session's log called the marker "critical-path #1"; it was #1 at the time. The table has since renumbered twice. **Numbers move; use the item name.**)*

Last session (prior): **2026-07-29 (early)** — the install tutorial.

Last session (prior): **2026-07-28** — Forge design, zero code.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for EVERY role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a six-step creator first run ending in the install tutorial, eight non-creator first-run screens, and **redemption approval that records the adult who actually signed off.**

**The creator sequence:** add-family → first-quest → stock-vault → the-hold → recap → add-to-home → board.
**The adult joiner sequence (verified on the glass):** board → approving → vault → pin → quests → the real board.
**The kid joiner sequence:** three beats, built, not yet walked by an actual kid.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **`master-spec.md` owes ZERO folds.** Regenerated 2026-07-29 against a full read; amended 2026-07-30 against a full read for the shipped redemption fix, the corrected quest-approval posture, and the two-kinds-of-adult finding.

> **`north-star.md` is current.** One sentence still drifts: *"the auth email is now load-bearing in a way it wasn't"* — sender identity is fixed, only deliverability remains.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The marker's read and write, fixed as ONE change.** `FirstRunGate` reads via `getActiveMemberIdSync() ?? auth.uid()`; `mark_first_run_complete()` writes `WHERE id = auth.uid()` and `markFirstRunComplete` literally contains `void profileId;`. A kid sub-profile's row id is not any `auth.uid()`, so a kid's write **no-ops silently**. ⚠️ **The two bugs cancel — fixing the read alone produces an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend. **Confirmed independent of the redemption fix (D5).** | The kid joiner flow on shared devices. Gate D. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

**Off the critical path and household-scoped: Forge.** Design is a rest-period activity and costs no build lane.

---

## 🟢 CLOSED / RESOLVED — 2026-07-30

### The redemption `decided_by` fix
- ✅ **`approve_redemption` and `deny_redemption` re-signed to `(_redemption_id uuid, _approver_id uuid DEFAULT NULL)`.** Old one-arg signatures **dropped, not replaced** — a defaulted parameter added via `CREATE OR REPLACE` creates a second overload and makes every existing one-arg call ambiguous (`function is not unique`). Grants re-issued after the drop: `GRANT EXECUTE TO authenticated`, then `REVOKE FROM PUBLIC`, then `FROM anon`.
- ✅ **`_approver_id` NULL → `decided_by = auth.uid()`, exactly as before.** This is what keeps the Vault page working untouched.
- ✅ **`_approver_id` NOT NULL → validated server-side before use.** Must be in the caller's family and an active adult. **Raises on failure — no silent fallback to `auth.uid()`,** because a silent fallback makes a rejected approver look like a successful one.
- ✅ **Authority gate unmoved.** The session owner must still hold the parent role. The new parameter refines **attribution only** — it never grants authority.
- ✅ **`wall.tsx` sends `_approver_id: approverId`.** The value was already correct at that call site and was being dropped by the `mutationFn`'s destructuring one hop before the RPC. **The stale comment above the mutation — which described the old behavior as unavoidable — is replaced.**
- ✅ **Wall has no deny path**, so `deny_redemption` gained the parameter and no caller. Correct: sweep the class in the migration, don't invent a UI.
- ✅ **VERIFIED ON LIVE DATA.** Wall approval under the co-parent's PIN wrote `decided_by = 744982a0…` against `requested_by = b2cad7a8…` — the first non-account-holder value in the column's history. Every prior row stamped `9bdb0243…`.

### Recon findings that changed the design
- ✅ **`decided_by` has ZERO consumers.** Fetched by `select("*")`, rendered by nothing, branched on by nothing. The severity language was wrong and is corrected in the spec.
- ✅ **The bug was wall-only.** A join-code co-parent has their own `auth.uid()`, so the Vault page's hardcode names them correctly. **The kiosk is the only surface where one session serves two adults.**
- ✅ **THERE ARE TWO KINDS OF ADULT PROFILE.** Join-code adults have a matching `auth.users` row (`profiles.id = auth_user_id`); adults minted by `create_adult_profile` have a `profiles.id` with **no auth user at all**. This is now recorded in the spec — it invalidates any check that assumes an adult profile id is also a user id.
- ✅ **`parent_self_redeem` never calls `approve_redemption`** — separate INSERT, stamps `auth.uid()` at insert time, correct, untouched.
- ✅ **`wall_request_redemption` never touches the decision column.** Untouched.

---

## 🟡 PENDING VERIFY

- 🟡 **The Vault-page approval path is unverified post-migration.** Single adult, approve a pending kid redemption from the Vault page — should behave exactly as before via the NULL default. **Cheap, do it next session.**
- 🟡 **The kid joiner flow has never been walked by a kid.** **Blocked in practice on the shared-device path by critical-path #1.**
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in (quests).** Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits.
- 🟡 **Monthly post-fix is technically unexercised.** Narrow failure window.
- 🟡 **STALE chip predicate.** Likely `due_date < today`, which the two stranded past-due weeklies would fully explain.
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED FIVE TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS. *Sonnet · auto-accept OFF · read-only.*
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** — visual success criterion, Scott only.
- 🟡 **Avatar render fallback ("the floor")** — **check the wall specifically.**
- 🟡 **Founder tier-tag verification.** Flip gate ON, confirm **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, back in, confirm auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — now the test case for the timezone heal.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — the next design job

- ⬜ **🔴 UNAPPROVED WEEKLIES AND MONTHLIES NEVER ROLL FORWARD. Two are stranded on the live board** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted). **Recorded in the spec as a defect, not correct behavior.** ⚠️ **NEEDS A DESIGN PASS BEFORE A PROMPT** — the roll-forward rule for unapproved recurring quests is not decided, and a migration against an undecided rule is not writable. **The STALE chip predicate is probably the same fix.** Highest-value remaining item with a pixel attached.

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits.**
- ⬜ **The engine cannot accept a pre-session constraint.** **Scope with the rep-compensation job.**
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** Per-member flag when it breaks. Do not build now.
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **🔴 The marker read/write pair.** CRITICAL PATH #1.
- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by` now is.** `enforce_quest_update_authority` checks the *session owner's* role; `enforce_quest_family_refs` checks only same-family membership. **Neither checks that the written value names an adult** — a client can write a kid's profile id into `approved_by`. **Upgraded from a footnote: quest approval is now the weaker of the two paths, and redemption has the pattern to sweep toward.** Not urgent — intra-household under the walk-up boundary — but the spec no longer holds it up as the model.
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. ⚠️ **The 07-30 finding sharpens this: they are not interchangeable.** `has_role()` is only valid for auth-backed identities; `profiles.role` is authoritative for an arbitrary profile id.
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **`routeTree.gen.ts` was hand-edited, toolchain drift underneath.**
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.** **Quest Log applies no `due_date` filter — by design or by omission?** **Feed verb drift.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** *(Minor: `confirm()` copy inaccurate.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.**

**Fixed:**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.** Validated optional approver id; raises rather than falling back.
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage` (closed by deletion).

**Real, open:**
- ⬜ **`quests.approved_by` accepts any in-family profile id, including a kid's.** Intra-household under the walk-up boundary; the sweep target now that redemption is correct.
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — unreachable from this connection, platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Gate C privacy policy.**

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** Gate B. **Deserves its own careful pass.**
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.** **Now also includes:** the *"Testing redemption tracking"* quest, whatever reward Mia redeemed for the 07-30 verification, and the test holds from the 07-29 glass checks.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The marker read/write pair.** CRITICAL PATH #1.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **The stacked-Pip-voice line on the first setup screen** · **`points` surfacing as a user-facing noun** on `/first-run/adult/approving` · **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat: auto-default vs tappable "pick yours"** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

**⚠️ Sharpened 2026-07-30:** the boundary is why `quests.approved_by` accepting any in-family profile id is tolerable rather than critical. It is not why it is *correct*. Redemption now does better on the same surface, which is the argument for eventually sweeping quests toward it.

---

## 🔵 THE BUILD MODEL — holding

- **A 🔴 WITH NO CONSUMER IS NOT A 🔴. (NEW — 2026-07-30.)** The redemption item was carried as first-prompt-of-the-next-window on a breached-deadline framing and a PIN-screen promise. **Nothing read the column.** Second instance of *severity in a doc outliving its evidence*, and the first where **Scott caught it, not recon** — jAIne briefed execution instead of testing whether the item deserved the slot. **Before writing a prompt for a carried 🔴, ask what reads it.**
- **THE FIX WAS RIGHT AND THE REASON WAS WRONG. (NEW — 2026-07-30.)** It shipped because it cost ~1 credit, added zero taps, and stopped a recurring re-litigation — not because the deadline mattered. **Recording the real reason is what stops the next session re-arguing it from the old one.**
- **A PRESCRIPTION IN THE SPEC IS STILL A HYPOTHESIS. (NEW — 2026-07-30.)** `master-spec.md` said "quest approval's behavior is the correct one and redemption moves to match it." **D4 killed it** — quest approval never validates that `approved_by` names an adult. **A recon briefed to disprove killed a line in canon, which is the mechanism working.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID. (NEW — 2026-07-30.)** `create_adult_profile` mints profiles with no `auth.users` row. **`has_role()` would have false-negatived the exact co-parent the fix exists to record.** Lovable caught this; jAIne had flagged it as pedantry. **Two kinds of adult exist and any check that conflates them is wrong for one of them.**
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION. (NEW — 2026-07-30.)** `CREATE OR REPLACE` with an added `DEFAULT NULL` arg creates a **second overload**, and existing one-arg calls then fail as ambiguous. **Drop, recreate, re-grant.**
- **WHEN THERE IS NO GLASS, VERIFY THE DATA. (NEW — 2026-07-30.)** Nothing renders `decided_by`, so "verified on the glass" was structurally unavailable. **The standard is the live artifact, not the screen** — and an agent's shipped-report is neither.
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.** The marker's read and write are both account-holder-scoped. **Divergence announces itself; cancellation hides.**
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX — OR THE ONE THE FIX DEPENDS ON.** *(Extended 2026-07-30: the recon DO-NOT was scoped to edits, not reads, specifically so the profile-resolution surface could be examined without being touched. That is the correct shape.)*
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.** *(`deny_redemption` was a genuine class member and got the parameter; the wall deny UI was not invented to use it.)*
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.** Report the hashes first, before reading.
- **STALE LOCAL BITS MIMIC A MISSING FEATURE.**
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.** *(Nine reads were briefed where four would have done. Trimming AFTER Scott had already fired the brief is the wrong sequence for a right-sizing.)*
- **UNBUNDLE WELDED ASSUMPTIONS.** · **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.**
- **A GUARD THAT PROTECTS A ROUTE CAN BREAK THE FLOW THAT USES IT.** · **"INERT" IS NOT ONE BEHAVIOR.**
- **DELETING A FEATURE MADE THE PRODUCT BETTER.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.**
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.** · **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.** · **A prompt's DO-NOT-BUILD list is not self-enforcing.**
- **jAIne must not prescribe a check the frontend cannot perform.** · **State a hypothesis as a hypothesis, then read before asserting.**
- **A code revert is not a database revert.** Undo schema forward, always.
- **The docs are not the live codebase — and can diverge silently.** · **Hand-applied DB changes are forbidden.**
- **A Code job isn't done until the artifact is observable from outside the agent.** ⚠️ **jAIne cannot verify `theemberhold` hashes — the repo is private and `api.github.com` returns 403.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.** · **Sweep the class before fixing the instance.**
- **A defensive layer is not verified by the migration that adds it.** **HTTP 200 is not "renders."** **A preview is not prod.**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.** design-only · design + Lovable · design + Code · full.

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-30** — the redemption `decided_by` fix. `approve_redemption` + `deny_redemption` re-signed with a validated optional approver id, old signatures dropped and re-granted; `wall.tsx` stops discarding the PIN-verified adult. Verified on live data. Recon killed the spec's own prescription (D4) and jAIne's coupling hypothesis (D5).
- **2026-07-29 (late)** — the master-spec fold (seven folds, six corrections, Part II retitled) + the first-run completion marker + the arrival-gate exemption fix (`943a633`) + the onboarding dots-count fix.
- **2026-07-29 (early)** — the install tutorial.
- **2026-07-28** — Forge design session, zero code.
- **2026-07-27 (late)** — the non-creator first run: eight screens, two step arrays, arrival gate, route guards, the marker seam.
- **2026-07-27 (early/mid)** — the `families.timezone` update path and the hold-settings hierarchy pass.
- **2026-07-26** — table grants closed; `anon` at zero across fifteen tables. Five-screen Pip-guided first run. Signup rebuilt.
- **2026-07-25 (late)** — the constitution restructure.
- **2026-07-25 (early)** — the module reframe.
- **2026-07-23** — the household-local date seam. `families.timezone` + `household_today()`.
- **2026-07-21 (late)** — the SECURITY DEFINER grant surface.
- **2026-07-21 (evening)** — recurring-quest visibility. `isActiveQuest` unified.
- **2026-07-19** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.
- **2026-07-16** — roster "no members" root-caused. Pending→admission routing fixed.
- **2026-07-15** — admit-on-approval shipped. Live privilege-escalation fixed.
- **2026-07-14** — admit-on-approval data layer. Recurrence reworked to fixed calendar anchors.
- **2026-07-12** — Claude Code proven as a build lane. Auth hardening.
- **2026-07-11** — engine daily respawn verified. Avatar roster regenerated.
- **2026-07-10** — engine auto-approval, edit-form parity. XP killed. Vault rails.
- **2026-07-03 → 06-26** — Avatar Overhaul designed. Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, Campaigns, Calendar, the Vault, PIN, Quest Log.

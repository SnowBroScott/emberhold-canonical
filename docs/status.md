# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-29 (late)** — *the master-spec fold + the completion marker.* A design + Lovable + Code session. **`master-spec.md` regenerated against a full read — all seven owed folds closed.** Then, on 3.5 Lovable credits, the first-run completion marker shipped and **critical-path item #1 is closed.**

**The seven-fold pass is DONE and it was a read-then-regenerate, not a blind regen.** jAIne read all 455 lines of the current spec plus every `decisions.md` entry from 07-26 through 07-29 before writing a line. Six non-mechanical calls were surfaced for objection before the file was produced: Part II retitled from "SHAPE UNDECIDED" to Option-A-as-design-truth; the weekly/monthly lingering line reversed to a defect; rule 7's test case marked suspended rather than deleted; the COPPA shield qualified rather than removed; the `actor_label` prescription replaced with an explicit DO-NOT; the Forge onboarding branch written as designed-and-parked. **No objections raised.**

**The marker shipped and was walked end to end.** `profiles.first_run_completed_at`, backfilled in the same migration, written through a SECURITY DEFINER RPC, `EXECUTE` to `authenticated` then `PUBLIC` revoked, zero table grants on `profiles`. **The three-part tripwire shipped as one change** — the constant, the read body and the write body — which is the regression that would have hurt most.

**A live bug was created and caught on the first fresh-creator walk.** The arrival gate exempted `/first-run/` only. A brand-new creator is born with a NULL marker, the marker is role-agnostic, and the gate fires on every shell mount — so the creator was yanked out of `/onboarding/add-family` into the joiner tour. **Shipped live, caught in the same session, fixed by Claude Code at zero credits.** *(jAIne's DO-NOT-BUILD list fenced the exact file that needed the fix — the switch was armed and the panel it controls was taped over.)*

**Two pending-verifies closed in the same publish walk:** the install-tutorial prod walk (owed since 07-29 early) and the joiner flow's first real run.

Last session (prior): **2026-07-29 (early)** — the install tutorial. Manifest launch-polish, the Add-to-Home-Screen step, the `handoff` cut, first-screen copy.

Last session (prior): **2026-07-28** — Forge design, zero code. v1 inverted to prescription-first; the pre-session gate; the catalog as the single blocking dependency.

Last session (prior): **2026-07-27 (late)** — the joiner-flow session. Eight non-creator first-run screens built and published dark.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for EVERY role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a six-step creator first run ending in the install tutorial, and **eight non-creator first-run screens that are no longer dark.**

**The creator sequence:** add-family → first-quest → stock-vault → the-hold → recap → add-to-home → board.
**The adult joiner sequence (verified on the glass):** board → approving → vault → pin → quests → the real board.
**The kid joiner sequence:** three beats, built, not yet walked by an actual kid.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **`master-spec.md` owes ZERO folds.** Regenerated 2026-07-29 against a full read, then amended the same session for the marker's shipped shape, the answered RLS question, the kid-marker cancellation finding, the arrival-gate exemption rule, and the breached redemption deadline. **The next spec pass is not owed until new design truth accumulates.**

> **`north-star.md` is current.** One sentence still drifts: *"the auth email is now load-bearing in a way it wasn't"* — sender identity is fixed, only deliverability remains. Its own small pass, or fold it into the next spec session.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 Redemption approval must record the PIN-verified adult.** `approve_redemption` hardcodes `decided_by = auth.uid()`. **THE DEADLINE IS BREACHED** — the rule was "true before the arrival gate goes live," and the gate went live 2026-07-29. The `/first-run/adult/pin` screen names redemption explicitly. **First prompt of the next build window.** Lovable lane. | Gate B honesty. A live promise the product doesn't honor. |
| **2** | **🔴 The marker's read and write, fixed as ONE change.** `FirstRunGate` reads the account holder; `mark_first_run_complete()` writes `WHERE id = auth.uid()`. A kid sub-profile's row id is not any `auth.uid()`, so a kid's write **no-ops silently**. ⚠️ **The two bugs cancel — fixing the read alone produces an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend. | The kid joiner flow on shared devices. Gate D. |
| **3** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

**Off the critical path and household-scoped: Forge.** Design is a rest-period activity and costs no build lane. **Design is well ahead of the build — see the Forge section in `parking-lot.md`.**

---

## 🟢 CLOSED / RESOLVED — 2026-07-29 (late)

### The master-spec fold
- ✅ **All seven folds closed.** Creator onboarding flow and shell · signup posture · write-once timezone and its heal · Part II rescoped to household-only · non-creator first run + marker seam + arrival-state rule · Part II's v1 shape · the 07-29 onboarding-sequence change and PWA install door.
- ✅ **Six standing corrections landed.** The `actor_label` derive-from-`auth.uid()` prescription is now an explicit **DO-NOT** (it is a killed regression, and it sat in the doc three sessions on a summary nobody re-derived) · the "fourth activeness surface" note removed as dead · the `set_enabled_modules` "nothing has ever written that value" claim corrected to **false**, making the `'training'`→`'fitness'` change a data migration rather than a string edit · the COPPA shield qualified (partial — it does not cover join-by-code) · grant drift downgraded to a bounded platform residual · Part II's client-engine rule re-grounded on latency and sunk cost rather than connectivity.
- ✅ **The "empty board / no path to a first quest = top structural gap" line is GONE.** The doorway is built.
- ✅ **Part II retitled.** Option A is design truth; Option B is the only remaining fork.

### The completion marker
- ✅ **`profiles.first_run_completed_at timestamptz NULL`**, with the backfill (`SET … = now() WHERE … IS NULL`) in the **same migration** as the column add. No established household sees the flow.
- ✅ **`mark_first_run_complete()`** — SECURITY DEFINER, `search_path` pinned, `WHERE id = auth.uid() AND first_run_completed_at IS NULL`. Single-row and idempotent by construction.
- ✅ **Grants in the correct order:** `GRANT EXECUTE … TO authenticated` then `REVOKE EXECUTE … FROM PUBLIC`. **Zero table grants added on `profiles`.**
- ✅ **The tripwire held.** `FIRST_RUN_MARKER_AVAILABLE`, the real read, and the real write shipped in one change.
- ✅ **Both exits mark.** Terminal handoffs (`onboarding.recap`, `onboarding.add-to-home`, `first-run.adult.quests`, `first-run.kid.embers`) and `SetupShell.handleSkip()`. Non-blocking — navigation never waits on the write.
- ✅ **The RLS recon was skipped deliberately and it cost nothing.** Routing through an RPC works either way. *(The question is now answered anyway: a member CAN update their own `profiles` row — and it wouldn't have mattered, because that policy has the same `id = auth.uid()` ceiling.)*

### The arrival-gate exemption
- ✅ **Fixed.** `pathname.startsWith("/first-run/") || pathname.startsWith("/onboarding/")`. One line, Claude Code, zero credits, `origin/main` at `943a633`.
- ✅ **Verified four ways**, including the one the prompt never named: `/board` with a NULL marker **still redirects**. That path is the whole feature and it survived.

### Glass checks
- ✅ **Fresh creator signup — walked the full sequence to `add-to-home`.** Stays on `add-family`, no bounce.
- ✅ **Fresh join-by-code from a never-signed-in device — walked all five adult screens to the real board.**
- ✅ **Install tutorial prod walk — CLOSED.** Same publish pass.
- ✅ **Onboarding dots-count fixed and verified.** Creator 6, adult joiner 5. **The prompt was corrected before it ran** — jAIne's first draft told Lovable to derive from "a single source of truth," which would have collapsed three deliberately different registry lengths into one and broken a joiner trail that was already correct.

---

## 🟡 PENDING VERIFY

- 🟡 **The kid joiner flow has never been walked by a kid.** Three beats to the adult's five. Built, published, unexercised. **Blocked in practice on the shared-device path by critical-path #2.**
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in.** Code writes the PIN-verified adult (`adultId`), not the session owner. Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits.
- 🟡 **Monthly post-fix is technically unexercised.** Failure window: approving on the last evening of a month after UTC has rolled. Narrow.
- 🟡 **STALE chip predicate.** Likely `due_date < today`, which the two stranded past-due weeklies would fully explain.
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED FOUR TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS. *Sonnet · auto-accept OFF · read-only.*
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built. **The trigger has not fired.**
- 🟡 **The ember progress trail** — visual success criterion, Scott only.
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member. **Check the wall specifically.** *(Also the resolution path for the empty-roster-seat question.)*
- 🟡 **Founder tier-tag verification.** Flip gate ON, confirm **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. **Now the test case for the timezone heal.**
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.** Everything good in the module is downstream of it.
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Cannot express "175 instead of 185, so give me 8 instead of 5,"** now half the differentiator. **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits.** Parallel to the catalog.
- ⬜ **The engine cannot accept a pre-session constraint.** RPE autoregulation is backward-looking. **Scope with the rep-compensation job.**
- ⬜ **Equipment records need `exclusive` vs `shareable`.** One field, needed before contention resolution means anything.
- ⬜ **Rep-compensation needs a validity floor.** Below some deviation the app must say *"that's a different exercise now."* Unsized.
- ⬜ **The Smith machine offset is assumed, not measured.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag. Do not build it now.
- ⬜ **`enabled_modules` has no consumer.** Named future consumer is the gated `/forge` route.

## ⬜ OPEN — carried

- ⬜ **🔴 The redemption `decided_by` fix.** CRITICAL PATH #1.
- ⬜ **🔴 The marker read/write pair.** CRITICAL PATH #2.
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. **The `isActiveQuest` divergence pattern, second occurrence.** *(Distinct from the marker's cancellation problem — see `decisions.md`.)*
- ⬜ **What does `actor_label` mean?** `actor_id` is server-derived and truthful; only the rendered name is client-supplied. **Design call, needs Scott** — see parking-lot OPEN DECISIONS.
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.** Consistency question.
- ⬜ **`routeTree.gen.ts` was hand-edited, toolchain drift underneath.** **Probably self-healing**, but local `npm run dev`/`build` is a trap.
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.** No case in either feed consumer's switch.
- ⬜ **Unapproved weekly and monthly quests never roll forward.** **Two stranded** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted). **Now recorded in the spec as a defect, not as correct behavior.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY.** What changes is the curriculum.
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
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage` (closed by deletion).

**Real, open:**
- ⬜ **`approve_redemption` records the session owner, not the PIN-verified adult.** **Now a live broken promise** — CRITICAL PATH #1.
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — unreachable from this connection, platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.** Injury flags and body-weight numbers must not be ambient where guests walk. Fine for the Drapers' garage.
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Gate C privacy policy** — minors are real users.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Downgraded to tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, together.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** **Deliberately NOT bundled with the install tutorial — install never needed it.** Still makes "offline shows a themed shell" true for strangers, a Gate B item. **Deserves its own careful pass** — a botched offline cache is a silent-stale-content risk.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.** **Now includes the test holds created by this session's two glass checks.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Redemption `decided_by`.** CRITICAL PATH #1.
- ⬜ **The marker read/write pair.** CRITICAL PATH #2.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **The stacked-Pip-voice line on the first setup screen** · **`points` surfacing as a user-facing noun** on `/first-run/adult/approving` ("the points on that quest become real, spendable embers" — a string-law violation; queued for the free Haiku sweep) · **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

*(Removed from polish: onboarding dots-count — SHIPPED 2026-07-29.)*

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat: auto-default vs tappable "pick yours"** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS. (NEW — 2026-07-29.)** The marker's read and write are both account-holder-scoped. Wrong in the *same direction*, so nothing misbehaves — and fixing either alone produces an infinite loop. **Divergence announces itself; cancellation hides.** Distinct from the `isActiveQuest` pattern and mislabelling it as that would teach the wrong lesson.
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER. (NEW — 2026-07-29.)** The arrival gate exempted `/first-run/` because it was written for joiners, who never touch `/onboarding/`. **The creator case didn't exist until the constant flipped, and then it shipped live.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX. (NEW — 2026-07-29.)** jAIne's DO-NOT-BUILD list said "do not touch `FirstRunGate`," meaning the profile-resolution bug — but the route exemption lives in the same file. **The switch was armed and the panel it controls was taped over.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE. (NEW — 2026-07-29.)** jAIne's first dots prompt said "derive from a single source of truth," which would have collapsed three deliberately different registry lengths and broken a correct joiner trail. **The sweep instinct is right by default and wrong when the instances differ on purpose.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING. (NEW — 2026-07-29.)** Code's local clone was **16 commits stale** and found out at push time, not before reading. Clean rebase, fix re-verified, no harm — but the recon and the edit were both made against a stale file. **Same shape as the install screen "missing" on a stale device.**
- **STALE LOCAL BITS MIMIC A MISSING FEATURE.** Verify the deployed/pulled commit before diagnosing.
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.** The bloat instinct wears a thoroughness costume.
- **UNBUNDLE WELDED ASSUMPTIONS.** "Service worker = installable PWA" was two jobs fused in a doc.
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.**
- **A GUARD THAT PROTECTS A ROUTE CAN BREAK THE FLOW THAT USES IT.**
- **"INERT" IS NOT ONE BEHAVIOR.** *Don't fire* versus *don't block*.
- **DELETING A FEATURE MADE THE PRODUCT BETTER.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.** Read the consumer, not the helper.
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.**
- **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.**
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

- **2026-07-29 (late)** — the master-spec fold (seven folds, six corrections, Part II retitled) + the first-run completion marker (column, backfill, RPC, tripwire, both exits) + the arrival-gate exemption fix (`943a633`) + the onboarding dots-count fix. Creator and joiner flows both walked end to end on prod. Critical-path #1 closed.
- **2026-07-29 (early)** — the install tutorial. Manifest launch-polish; Add-to-Home-Screen step as terminal creator first-run screen; `handoff` cut with terminal wiring transplanted to `recap`; first-screen copy fixed; first-quest doorway confirmed built.
- **2026-07-28** — Forge design session, zero code. v1 inverted to prescription-first; the pre-session gate; the catalog named as the single blocking dependency; contention resolution; Forge display mode and the avatar-as-session-lane layout.
- **2026-07-27 (late)** — the non-creator first run: eight screens, two step arrays, arrival gate, route guards, the marker seam. Published dark.
- **2026-07-27 (early/mid)** — the `families.timezone` update path and the hold-settings hierarchy pass.
- **2026-07-26** — table grants closed; `anon` at zero across fifteen tables. Five-screen Pip-guided first run. Signup rebuilt; the `pending_setup` stash and its plaintext PIN deleted.
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

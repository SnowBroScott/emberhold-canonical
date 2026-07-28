# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-28** — *a design-only session, conducted from a gym floor with a live user in the room.* **Net code delta: zero. Net doc delta: status and parking-lot replaced, thirteen `decisions.md` entries. Lovable credits spent: zero.**

**Forge v1 got inverted, and a real user did it.** Asked what they valued most about Fitbod, Scott and May both named *what exercise to do* and *what weight to use*; Scott added the rest timer. **Neither of them named logging.** The log is the commodity — every free tier does it well. **Forge v1 is therefore a prescription engine with a log attached, not a logger with programming bolted on.** That reorders the entire build.

**The differentiator landed on top of the #1 feature by luck.** If the whole value is *"tell me what to do,"* trust in the instruction **is** the product — and the most-cited complaint about the incumbent is that it won't explain itself. The reason string stops being a nice touch and becomes the moat, sitting directly on the most-valued feature.

**May named the override condition, and it is two findings wearing one complaint.** She overrides Fitbod when (a) an injury needs accounting for, or (b) a bad night's sleep means there is no 100% available — *"it always assumes 100% all the time."* **A readiness scalar and an injury filter are different controls with different lifespans** and must never be one dial. She separately flagged that core is always programmed as filler — **a template bias, not a modeling failure, and free to fix.**

**Scott solved flag expiry without a timer.** Re-ask at the next session that touches the flagged region — *"last push day you said your chest was hurt. Is it still injured?"* **The training split is the expiry mechanism.** No decay logic, no duration declared at flag time. The answer is three-way: still hurt / better / cleared.

**The single blocking dependency is now named and it is not ambiguous: the exercise catalog.** Movement pattern, muscle attribution, equipment requirement, substitution map. **Injury substitution, rack-taken swaps, resequencing, and the pre-session gate's relevance check all ride the same tagging layer.** Four features, one dependency. It is first.

**Contention resolution is the first Forge feature no competitor could ship.** Scott and May train together on different splits and collide on shared equipment. Two people, one tenant, one inventory, generated together — Fitbod has no concept of the person twelve feet away. **The resolution ladder is resequence → substitute → modify load**; most collisions die at tier one for free.

**Scott corrected a LOCKED rationale and was right.** The client-side-engine rule is stated in `master-spec.md` as resting on garage connectivity. For a bespoke two-person build, connectivity is never a concern. **The rule survives on latency and sunk cost, not on connectivity** — and the correction unlocks two things: a Supabase-hosted catalog editable without redeploy, and live mid-session regeneration.

**Forge is still household-scoped and still off the critical path.** Nothing in this session moves a gate.

Last session (prior): **2026-07-27 (late)** — the joiner-flow session. Eight non-creator first-run screens built and published dark. A live regression created and caught in the same session.

Last session (prior): **2026-07-27 (early)** — design and recon. `append_activity` downgraded; Forge scoped to the Draper household.

Last session (prior): **2026-07-26** — table grants closed, `anon` at zero across fifteen tables. Five-screen Pip-guided first run shipped and cold-walked.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for creators and a built-but-dark one for everyone else.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model with a working update path, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a five-screen creator first run, and eight non-creator first-run screens behind an inert gate.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness is the sole module, it is not built, and it is scoped to the Draper household.**

> **`master-spec.md` owes SIX folds.** The creator onboarding flow · the signup posture · the write-once timezone and its heal · Part II's rescoping to household-only · the non-creator first run, including the completion-marker seam and the arrival-state rule · **and Part II's v1 shape from 07-28 — the prescription-first inversion, the pre-session gate, the catalog dependency, contention resolution, and the display-mode surface.** Plus corrections found on the 07-27 read: the `actor_label` derive-from-`auth.uid()` prescription is a killed regression, the "fourth activeness surface" note is dead, the `set_enabled_modules` "nothing has ever written that value" claim is false, the Onboarding section still describes the empty-board gap as the top structural problem, and the COPPA "kids have no auth identity" shield is false for a join-by-code minor. **Plus one now: Part II's client-engine rule cites connectivity as its reason, and that reason is weaker than written — it survives on latency and sunk cost.** **jAIne has read the current version — a real regeneration is available and is the next scheduled work.**

> **`north-star.md` is current.** One sentence is drifting: *"the auth email is now load-bearing in a way it wasn't"* was written when the entire signup path depended on it. Sender identity is fixed and only deliverability remains. Fold on the same pass.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The first-run completion marker.** A `profiles` column plus a write path. **The eight screens are dark until it lands.** ⚠️ **Answer the RLS question before scoping the build: can a member UPDATE their own `profiles` row?** If not, this is a column *plus* an RPC, not a column. Lovable lane. | Gate D. Activation for everyone who isn't the hold creator. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

**Off the critical path and household-scoped: Forge.** Design is a rest-period activity and costs no build lane. Build work waits for V1. **Design is now well ahead of the build — see the Forge section below.**

---

## 🟢 CLOSED / RESOLVED — 2026-07-28 (Forge design)

### The v1 inversion

- ✅ **Forge v1 is a PRESCRIPTION ENGINE with a log attached.** Both users named "what exercise" and "what weight." Neither named logging. **The log is table stakes; the prescription is the product.**
- ✅ **"What weight" is already written.** `progression.ts` does RPE autoregulation, plate-snapping against inventory, Epley+RIR e1RM, ten tests, zero Supabase imports.
- ✅ **"What exercise" does not exist anywhere, and it is the harder half.** Previously carried as a footnote (*"no progression axis except load"*); it is now half of the single most-valued feature.
- ✅ **The rest timer is not polish.** Deterministic, trivial, and it is the reason the phone is in hand between sets. **The in-session habit hook.**

### The pre-session gate

- ✅ **One screen, before generation, that accepts declared constraints.** `progression.ts` autoregulates on RPE, which is a **backward** signal — it corrects one session late, on exactly the day it was needed. Nothing in the current asset accepts a constraint *before* prescribing.
- ✅ **Readiness is a SCALAR; injury is a FILTER.** Tired dials the session down. Injured excludes a movement at any weight and persists across sessions. **Different lifespans, different controls, never one dial.**
- ✅ **An injury flag carries its own resolution, asked at flag time.** *Reduced load / avoid entirely.* **One extra tap kills a severity-modeling problem.**
- ✅ **"Reduced load" is nearly free; "avoid entirely" is the expensive one** — avoiding creates a hole and something must fill it. That is the catalog.
- ✅ **Flag expiry is the training split, not a timer** *(Scott's call)*. Re-ask at the next session that touches the flagged region. **Three-way answer — still hurt / better / cleared.** "Better" moves avoid→reduced, reusing the fork already invented.
- ✅ **No HealthKit is fine here, arguably better.** A declared rough night is more honest than an inferred score, and the user owns the result instead of arguing with it.
- ✅ **Core-as-filler is a template bias, not a modeling failure.** Free to fix and a visible "we're not that app" flag in week one.

### The catalog — the single blocking dependency

- ✅ **Movement pattern + muscle attribution + equipment requirement + substitution map.** **Four features ride it:** injury substitution, rack-taken swaps, resequencing, and the pre-session gate's relevance check (*"last push day you said your chest was hurt"* requires knowing today's session touches chest).
- ✅ **Make is an OFFLINE CONTENT FACTORY, never a runtime.** Generate the catalog and a template library, **Scott reviews**, ship as data. Same pattern the spec already uses for vignette assets: generate offline, select locally, instant on the glass.
- ✅ **It can live in Supabase rather than as a static asset** — editable without a redeploy, which matters while the substitution map is being tuned.

### Bespoke economics

- ✅ **Household scope kills the free/paid split, per-user marginal cost, and quota pressure — and changes the architecture not at all.**
- ✅ **Generation is a session-start act, not a per-set act.** The model writes the plan; the client executes it. **Mid-session regeneration is viable** now that connectivity is not treated as a constraint.
- ✅ **The API key lives in a Supabase edge function, not the client.** theemberhold.com is public. Bespoke buys no shortcut here.
- ✅ **The deterministic template path is retained as the no-generation fallback** — same engine, double duty.
- ✅ **The reason string stays COMPUTED, never narrated.** *"Avg RPE 6.7, adding 10 lb"* is trustworthy because it is derived. The model may pick exercises and structure; it may not narrate math it did not do.

### Contention resolution

- ✅ **Two people, one tenant, one shared inventory — structurally unavailable to any competitor.** Rule 5 already has the module reading both members from the existing member table.
- ✅ **The ladder is RESEQUENCE → SUBSTITUTE → MODIFY LOAD.** Pulling something forward from your own session costs zero integrity. Most collisions die at tier one.
- ✅ **Bias, don't schedule.** Both sessions generate from one call; stagger the openers. **No presence detection, no equipment sensing** — the users are ten feet apart and can see the rack.
- ✅ **Equipment needs one new field: exclusive vs. shareable.** Smith and squat rack are exclusive; dumbbells at different weights are parallel.

### Time budget

- ✅ **A time budget is an INPUT; a schedule is an OUTPUT.** The number shapes what gets generated — five exercises instead of eight. **Its job is sizing, not prediction**, which is why Fitbod's is useful while being inaccurate.
- ✅ **The rest timer calibrates the estimate for free.** Real set-to-set intervals, per person, over months. Fitbod estimates from population averages. **A byproduct of data already being collected, not a feature to build.**
- ✅ **Shared-equipment time tax needs no model.** If real sessions run 12% over, calibration learns 12%.
- ✅ **Nag with the rest timer; never with session duration** *(Scott's call)*. **Interrupt only when there is an action attached.** "Go" is actionable; "you're 20 minutes over" is a verdict you cannot act on.

### Forge display mode

- ✅ **A separate garage device, sharing flavor and components with the wall but doing a different job.** The wall is glanceable and passive; the Forge screen is touched, wet-handed, mid-rest. **Not a toggle of the existing display mode.**
- ✅ **Idle defaults to the wall; a workout takes it over.** Precedence, not a new mode. **Manual start / end. No presence detection.** Pervasive presence in more rooms is a retention win on the existing thesis.
- ✅ **An always-on screen removes the iOS background-timer problem entirely.** A real technical argument, not just a nice-to-have — iOS Safari suspends JS timers in the background and PWA push is fenced.
- ✅ **THE AVATAR IS THE SESSION LANE** *(Scott's design)*. Reuses the display-mode roster + agenda layout and the existing hero-portrait component per rule 5. Tap avatar → pre-session questions → **Start** → the lane expands into an agenda line carrying exercise / load / reps → tap the agenda item → log the set → rest timer appears. **Multiple lanes run this concurrently.** This is the answer to two people, two splits, two timers on one screen — **not split view.**
- ✅ **Free feature falls out of it:** both agendas visible side by side means you *see* the collision two lines before it happens. Contention becomes avoidable on sight rather than resolved after the fact.
- ✅ **The wall's never-mints-never-edits rule does NOT bind Forge.** That rule protects the **ember economy**; Forge has no economy — constitutional rule 2 is the entire reason it is a module and not a register. **A Forge screen that writes sets is operating outside the surface the rule protects.** Recorded so it is not re-litigated.
- ✅ **The pre-session gate moves from phone/kitchen to the garage screen.** One device, one place, and generation latency lands while the user is standing there anyway. **Supersedes the kitchen framing settled earlier the same session.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 Signup checks #2 and #3.** (2) Cold join-path signup — no PIN field, no error toast, lands as pending. (3) Original-tab path — sign up, ignore the email, press "I confirmed," sign in; **should land on finish-setup identically to check #1.**
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** Verification requires hardware nobody has. **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in.** The code writes the PIN-verified adult (`adultId`), not the session owner. Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits and balance moves.
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

## ⬜ OPEN — Forge, new from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.** Everything good in the module is downstream of it.
- ⬜ **🔴 `progression.ts` has no progression axis except load.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo. **It cannot express "175 instead of 185, so give me 8 instead of 5,"** which is now half the differentiator. **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits.** Independent of the catalog — can run in parallel.
- ⬜ **The engine cannot accept a pre-session constraint.** RPE autoregulation is backward-looking. The pre-session gate needs a forward input path into prescription. **Scope with the rep-compensation job.**
- ⬜ **Equipment records need `exclusive` vs `shareable`.** One field, needed before contention resolution means anything.
- ⬜ **Rep-compensation needs a validity floor.** Below some deviation the app must say *"that's a different exercise now."* Carried, still unsized.
- ⬜ **The Smith machine offset is assumed, not measured.** Two minutes with a known weight. **A wrong offset silently corrupts every Smith e1RM forever, unreconstructably.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag. Do not build it now.
- ⬜ **Module navigation is unresolved.** Seven tabs is one past mobile comfort. **Not "add another tab."** **Forge's contained-route posture defers this entirely.**
- ⬜ **`enabled_modules` has no consumer.** Seventh session carrying it. Named future consumer is the gated `/forge` route.

## ⬜ OPEN — carried

- ⬜ **`approved_by` vs `decided_by` — RESOLVED IN DIRECTION, UNBUILT.** Quest approval's behavior wins; redemption aligns to it.
- ⬜ **🔴 `FirstRunGate` reads the ACCOUNT HOLDER, not the switched-into profile.** On a shared device a kid switching in is checked against the account holder's marker and role. **Fix before the marker column lands or the kid flow never runs for the people it was built for.**
- ⬜ **🔴 THE MARKER TRIPWIRE — one change, three parts.** `FIRST_RUN_MARKER_AVAILABLE`, `hasCompletedFirstRun`, and `markFirstRunComplete` must go live together, or the `/onboarding/*` regression returns. **This belongs in the build brief, not in a code comment.**
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. **The `isActiveQuest` defect pattern re-appearing.**
- ⬜ **Redemption approval must move to match the PIN screen's promise.** The string is live and says the PIN ties a redemption to whoever signed off. **Deadline: true before the arrival gate goes live.**
- ⬜ **The optimistic `true` will flash.** Cosmetic, fixable when the marker is real.
- ⬜ **What does `actor_label` mean?** `actor_id` is server-derived and truthful; only the rendered name is client-supplied. **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.** Consistency question, not a permission one.
- ⬜ **`routeTree.gen.ts` was hand-edited, and there is toolchain drift underneath it.** **Probably self-healing**, but local `npm run dev`/`build` is a trap.
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.** No case in either feed consumer's switch.
- ⬜ **The COPPA shield in `master-spec.md` is false for a real path.** A minor joining by code has an email, a password, and an auth row. **The sentence is wrong and must be qualified in the fold.**
- ⬜ **Unapproved weekly and monthly quests never roll forward.** **Two stranded right now** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted).
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY.** What changes is the curriculum.
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.** **Quest Log applies no `due_date` filter — by design or by omission?** **Feed verb drift.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.** *(Minor: `confirm()` copy is inaccurate.)*
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.** Real tier set by parent-gated `admit_pending_member`.
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
- ⬜ **NEW — Forge display mode is a semi-public surface.** The garage screen is the second room-visible surface after the wall. **Injury flags and body-weight numbers must not be ambient where guests walk through.** Fine for the Drapers' own garage; a real question the day this meets strangers.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Downgraded to tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, and together.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true. **Its Forge-driven priority bump is now weaker** — connectivity was overstated as a Forge constraint. It still matters for strangers.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The first-run completion marker.** CRITICAL PATH #1.
- ⬜ **`FirstRunGate` profile-switch bug.** Ships with the marker or the kid flow is dead on arrival.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · **injury-prescription liability posture at stranger scale (NEW)** · **commercial-gym equipment model (NEW)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **ASK THE USER WHAT THEY VALUE, THEN NOTICE WHAT THEY DIDN'T SAY. (NEW.)** Two users named prescription; neither named logging. **The omission reordered the build.** The thing everyone assumes is the product is often the commodity.
- **A COMPLAINT CAN BE TWO FINDINGS WEARING ONE SENTENCE. (NEW.)** *"It always assumes 100%"* is a readiness scalar **and** an injury filter — different lifespans, different controls. Merging them into one dial produces something wrong for both.
- **LET THE FLAG CARRY ITS OWN RESOLUTION. (NEW.)** Asking *reduced load or avoid entirely* at flag time deletes a whole severity-modeling problem for one tap.
- **AN EXISTING RHYTHM BEATS A NEW TIMER. (NEW — SCOTT'S.)** Flag expiry rides the training split instead of a decay mechanism. **Look for a cadence the product already has before building one.**
- **A LOCKED RULE'S STATED REASON CAN BE WRONG WHILE THE RULE IS RIGHT. (NEW — SCOTT'S CORRECTION.)** The client-engine rule cites garage connectivity; it survives on latency and sunk cost. **jAIne quoted the rationale instead of testing it.** Re-derive, don't recite.
- **GENERATE OFFLINE, SELECT LOCALLY. (NEW.)** An LLM belongs in the factory, not the runtime. Same pattern the vignette pipeline already uses.
- **A GUARD THAT PROTECTS A ROUTE CAN BREAK THE FLOW THAT USES IT.** Before adding a guard, name every consumer of the route it guards.
- **"INERT" IS NOT ONE BEHAVIOR.** *Don't fire* versus *don't block*. One predicate, two meanings.
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
- **A defensive layer is not verified by the migration that adds it.** **HTTP 200 is not "renders."**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.** design-only · design + Lovable · design + Code · full.

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-28** — Forge design session, zero code. v1 inverted to prescription-first; the pre-session gate; the catalog named as the single blocking dependency; contention resolution; Forge display mode and the avatar-as-session-lane layout.
- **2026-07-27 (late)** — the non-creator first run: eight screens, two step arrays, arrival gate, route guards, the marker seam. Published dark. A live regression created and caught in the same session.
- **2026-07-27 (early/mid)** — the `families.timezone` update path and the hold-settings hierarchy pass. Two read-only Code recons.
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

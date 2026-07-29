# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-29** — *the install-tutorial session.* Built the Pip-guided Add-to-Home-Screen step, cleaned the creator first-run flow, and cut a redundant feature-tour screen. **A design + Lovable session. Verified on the glass in the Lovable preview. Publish-to-prod walk still owed.**

**The install door was already 90% built — the recon proved it twice.** Live-head inspection found a valid manifest (`name`, `short_name`, `display: standalone`, `start_url: /`, 192 + 512 icons) and correct iOS tags (`apple-mobile-web-app-capable=yes`, `apple-touch-icon` → `/icon-180.png`, `apple-mobile-web-app-title=Emberhold`, `theme-color=#1A110B`). Install and standalone launch already worked. **What was missing was guidance, not machinery** — nobody knew to install, and on iOS the gesture is non-obvious. So this was never a "build PWA infrastructure" job; it was "show people the door that already exists."

**The service worker got unbundled from install, and it matters.** `status.md` had welded "service worker" to "makes installable PWA true." They are two jobs. Manifest + apple-tags deliver **presence** (home-screen install, standalone launch); the SW delivers **offline resilience** — a separate Gate B stranger-proofing item. On iOS — most of the beta — there is no `beforeinstallprompt` and the SW does nothing for install; the flow is Share → Add to Home Screen regardless. **The tutorial ships without an SW. The SW stays in Gate B, and it earns its own careful pass** — a botched offline cache is exactly the silent-stale-content failure this project is paranoid about. *(See `decisions.md` 2026-07-29.)*

**First-run had drifted back into a feature tour, and the drift got cut.** `master-spec` is explicit: first-run is contextual setup, not a feature tour; the four-beat loop card belongs to the landing page and on-demand help. Two tour screens had wedged in. **"Now show them" (the loop card restated a THIRD time in one flow, built as `handoff.tsx`) was cut. "The rest of your hold" (the Lists/Calendar/Campaigns showcase) was KEPT on a deliberate call** — it's the one moment that signals Emberhold isn't only a chore game, which points straight at the thesis. *(See `decisions.md` 2026-07-29 — and do not "finish the cleanup" by cutting it later; the deviation is intentional.)*

**The cut had a trap the free Code recon caught.** "Now show them" (`handoff.tsx`) carried the flow's terminal navigation — its `finish()` routed to `recap` → which runs the `isStandaloneInstalled()` check → which is what reaches the install screen at all. A naive delete would have **severed the chain to the install tutorial**. The build transplanted that wiring onto `recap`. Verified on the glass: chain holds, install screen shows, "Now show them" gone.

**The first-quest doorway is confirmed BUILT.** Both `master-spec` and this board have long called "a new hold lands on an empty board with no path to a first quest" the *top structural gap*. It's closed — the "Post your first quest" screen exists, pre-filled chips, "Light the quest," verified in the flow. **That line in `master-spec` is now stale and is a fold, not a build.**

**Nothing this session moved a gate.** The install tutorial is a **Gate D** funnel item built ahead of Gates B and C completing. Good to have banked; it does not change the ladder.

Last session (prior): **2026-07-28** — Forge design, zero code. v1 inverted to prescription-first; the pre-session gate; the catalog named as the single blocking dependency; contention resolution; Forge display mode and the avatar-as-session-lane layout.

Last session (prior): **2026-07-27 (late)** — the joiner-flow session. Eight non-creator first-run screens built and published dark. A live regression created and caught in the same session.

Last session (prior): **2026-07-26** — table grants closed, `anon` at zero across fifteen tables. Five-screen Pip-guided first run shipped and cold-walked.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for creators — now including a Pip-guided install tutorial as the terminal first-run step — and a built-but-dark one for everyone else.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a five-screen creator first run **ending in the Add-to-Home-Screen tutorial**, and eight non-creator first-run screens behind an inert gate.

**The creator first-run sequence is now:** add-family → first-quest → stock-vault → the-hold ("the rest of your hold") → recap ("Welcome to Emberhold") → **add-to-home** → board. Five setup steps then the install step. `handoff` ("Now show them") removed.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **`master-spec.md` owes SEVEN folds now.** The six already tracked — creator onboarding flow · signup posture · write-once timezone and its heal · Part II's rescoping to household-only · the non-creator first run (completion-marker seam + arrival-state rule) · Part II's v1 shape from 07-28 — **plus a seventh: the 2026-07-29 onboarding-sequence change** (the `handoff` cut, the install tutorial as the terminal first-run step, the first-screen copy fix, and the confirmation that the first-quest doorway is BUILT — which kills the "empty-board gap = top structural problem" line). Plus the standing corrections: `actor_label` derive-from-`auth.uid()` is a killed regression, the "fourth activeness surface" note is dead, the `set_enabled_modules` "nothing has ever written that value" claim is false, the COPPA "kids have no auth identity" shield is false for a join-by-code minor, and Part II's client-engine rule cites connectivity when it rests on latency and sunk cost. **jAIne has read the onboarding + Part-I slices this session but NOT the full current doc — a real regeneration needs a dedicated spec pass, not a blind regen.**

> **`north-star.md` is current.** One sentence still drifts: *"the auth email is now load-bearing in a way it wasn't"* — sender identity is fixed, only deliverability remains. Fold on the spec pass or its own pass. Nothing 2026-07-29 touched it.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The first-run completion marker.** A `profiles` column plus a write path. **The eight non-creator screens are dark until it lands.** ⚠️ **Answer the RLS question before scoping: can a member UPDATE their own `profiles` row?** If not, this is a column *plus* an RPC. Lovable lane. **Untouched 2026-07-29 — creator flow only this session.** | Gate D. Activation for everyone who isn't the hold creator. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

**Off the critical path and household-scoped: Forge.** Design is a rest-period activity and costs no build lane. Build waits for V1. **Design is well ahead of the build — see the Forge section in `parking-lot.md`.**

---

## 🟢 CLOSED / RESOLVED — 2026-07-29 (the install tutorial)

### The install door — already real
- ✅ **Valid manifest already live.** `name`, `short_name`, `description`, `start_url: /`, `display: standalone`, portrait, 192 + 512 ember-castle icons.
- ✅ **iOS meta tags already correct.** `apple-mobile-web-app-capable=yes`, `apple-touch-icon`→`/icon-180.png` (180×180), `apple-mobile-web-app-title=Emberhold`, `theme-color=#1A110B`. **iPhone install launches a real standalone app, not a Safari-tab bookmark.**
- ✅ **Launch polish shipped.** `background_color` set to `#1A110B` (was unset → white splash flash on launch) and `apple-mobile-web-app-status-bar-style=black-translucent` added (was absent → white iOS status bar). Boot is now ember-dark edge-to-edge.

### The tutorial
- ✅ **ONE screen, appended as the terminal step of creator first-run.** Not a route, not a modal fired from elsewhere, not a state machine. Rides the walkthrough that already exists. *(Right-sized down from a nine-illustration, four-branch sketch — out-habit, don't out-feature, applied to onboarding.)*
- ✅ **A real captured iOS share-sheet screenshot, framed in a charred "forge-window" ember border** — glow bleeding inward from the edges, top-edge catch-light. Not a painted illustration. Landed on the first border pass. **Do not modify — `onboarding.add-to-home.tsx`.**
- ✅ **Platform-conditional copy.** iOS: "Tap Share, then Add to Home Screen" + the screenshot. Android/desktop: caption only — "Open your browser's menu and choose Install app."
- ✅ **The webview-escape line.** "Not seeing the Share button? Open theemberhold.com in Safari first." **This is the single most likely silent failure** — invite links open in Messages/Gmail webviews where the Share→Add option does not exist.
- ✅ **Suppress-if-installed.** `display-mode: standalone` / `navigator.standalone` — never shows to someone who already installed.
- ✅ **iPad routing baked in** — iPadOS reports a "Macintosh" UA, so a Mac UA with `maxTouchPoints > 1` routes to the iOS branch.

### The onboarding-flow cleanup
- ✅ **"Now show them" (`handoff.tsx`) cut** — the loop card restated a third time. Removed from `SETUP_STEPS`, route deleted.
- ✅ **Its terminal wiring transplanted onto `recap`** so the chain to `add-to-home` survives. **This was the trap:** handoff carried the forward navigation and the `isStandaloneInstalled()` handoff to the install screen. Verified the chain holds end-to-end on the glass.
- ✅ **"The rest of your hold" (the-hold) KEPT** — deliberate deviation from "setup not tour," on the thesis-signal argument.
- ✅ **First-screen copy fixed** — "Your household's quest board / Your whole home, finally in one place" (a tagline over a name+avatar form) → **"Who's tending the hold? / Your name, your face, and your role in the hold."** Task instruction, not a marketing headline.
- ✅ **The replay-intro handler** — "Replay the intro" in Pip's Help now launches the intro walkthrough rather than re-opening the help menu.
- ✅ **Welcome copy conditional** — first-run reads "Welcome to Emberhold"; "back" only on replay.
- ✅ **Loading copy deduped** — the transition screen no longer stacks a second "one more" against the install screen's "one more thing."
- ✅ **The first-quest doorway is BUILT** — confirmed in the flow. Closes the long-standing "top structural gap." *(A `master-spec` fold.)*

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 Install tutorial — publish-to-prod + prod walk.** Verified in the **Lovable preview** (final screenshots show the `lovable.dev` editor). **Not confirmed published to theemberhold.com.** Publish, then walk creator first-run in a **plain Safari tab** (not the installed icon, or the suppress hides it) all the way to `add-to-home` → board. *This is the "verify the live artifact, not the preview" gate.*
- 🟡 **Dots-count cosmetic.** After the `handoff` cut, `recap` shows a **7-segment** trail while `add-to-home` shows **6** — `recap` is still counting the old `SETUP_STEPS.length`. Harmless, one-line fix whenever next in that file. Polish.
- 🟡 **🔴 Signup checks #2 and #3.** (2) Cold join-path signup — no PIN field, no error toast, lands as pending. (3) Original-tab path — sign up, ignore the email, press "I confirmed," sign in; should land on finish-setup identically to check #1.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in.** Code writes the PIN-verified adult (`adultId`), not the session owner. Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits.
- 🟡 **Monthly post-fix is technically unexercised.** Failure window: approving on the last evening of a month after UTC has rolled. Narrow.
- 🟡 **STALE chip predicate.** Likely `due_date < today`, which the two stranded past-due weeklies would fully explain.
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED THREE TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS. *Sonnet · auto-accept OFF · read-only.*
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built. **The trigger has not fired.**
- 🟡 **The ember progress trail** — visual success criterion, Scott only.
- 🟡 **Avatar render fallback ("the floor")** — pull up a not-yet-re-forged member. **Check the wall specifically.** *(Now also the resolution path for the empty-roster-seat question — see parking-lot OPEN DECISIONS.)*
- 🟡 **Founder tier-tag verification.** Flip gate ON, confirm **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. **Now the test case for the timezone heal.**
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.** Everything good in the module is downstream of it.
- ⬜ **🔴 `progression.ts` has no progression axis except load.** `NextLoad` returns `{loadLb, reason, holdsAfter}` — no reps, no sets, no tempo. **Cannot express "175 instead of 185, so give me 8 instead of 5,"** now half the differentiator. **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits.** Parallel to the catalog.
- ⬜ **The engine cannot accept a pre-session constraint.** RPE autoregulation is backward-looking. The pre-session gate needs a forward input path. **Scope with the rep-compensation job.**
- ⬜ **Equipment records need `exclusive` vs `shareable`.** One field, needed before contention resolution means anything.
- ⬜ **Rep-compensation needs a validity floor.** Below some deviation the app must say *"that's a different exercise now."* Unsized.
- ⬜ **The Smith machine offset is assumed, not measured.** Two minutes with a known weight. **A wrong offset corrupts every Smith e1RM forever, unreconstructably.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.** When it breaks it is a per-member flag. Do not build it now.
- ⬜ **Module navigation is unresolved.** Seven tabs is one past mobile comfort. **Forge's contained-route posture defers this entirely.**
- ⬜ **`enabled_modules` has no consumer.** Named future consumer is the gated `/forge` route.

## ⬜ OPEN — carried

- ⬜ **`approved_by` vs `decided_by` — RESOLVED IN DIRECTION, UNBUILT.** Quest approval's behavior wins; redemption aligns.
- ⬜ **🔴 `FirstRunGate` reads the ACCOUNT HOLDER, not the switched-into profile.** On a shared device a kid switching in is checked against the account holder's marker and role. **Fix before the marker column lands or the kid flow never runs for the people it was built for.**
- ⬜ **🔴 THE MARKER TRIPWIRE — one change, three parts.** `FIRST_RUN_MARKER_AVAILABLE`, `hasCompletedFirstRun`, and `markFirstRunComplete` go live together or the `/onboarding/*` regression returns. **In the build brief, not a code comment.**
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. **The `isActiveQuest` defect pattern re-appearing.**
- ⬜ **Redemption approval must move to match the PIN screen's promise.** The string is live. **Deadline: true before the arrival gate goes live.**
- ⬜ **What does `actor_label` mean?** `actor_id` is server-derived and truthful; only the rendered name is client-supplied. **Design call, needs Scott** — see parking-lot OPEN DECISIONS.
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.** Consistency question.
- ⬜ **`routeTree.gen.ts` was hand-edited, toolchain drift underneath.** **Probably self-healing**, but local `npm run dev`/`build` is a trap.
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.** No case in either feed consumer's switch.
- ⬜ **The COPPA shield in `master-spec.md` is false for a real path.** A minor joining by code has an email, password, and auth row. **Qualify in the fold.**
- ⬜ **Unapproved weekly and monthly quests never roll forward.** **Two stranded** — *Grocery Shopping* (07-06, claimed) and *Take out the trash* (07-21, submitted).
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
- ⬜ **Forge display mode is a semi-public surface.** Injury flags and body-weight numbers must not be ambient where guests walk. Fine for the Drapers' garage; a real question at stranger scale.
- ⬜ **NEW — `flock.js` analytics tracker in the app `<head>`** (self-proxied `/~api/analytics`, likely Lovable-injected). **Must be named in the Gate C privacy policy** — minors are real users. *(See `decisions.md` 2026-07-29, NOTED.)* Not a this-session problem.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Downgraded to tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision, together.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** **Deliberately NOT bundled with the install tutorial — install never needed it** (2026-07-29). Still makes "offline shows a themed shell" true for strangers, a Gate B item. **Deserves its own careful pass** — a botched offline cache is a silent-stale-content risk.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — deserves its own session.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The first-run completion marker.** CRITICAL PATH #1.
- ⬜ **`FirstRunGate` profile-switch bug.** Ships with the marker or the kid flow is dead on arrival.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.** Only the member who logs in is prompted.

## ⬜ OUTSTANDING — polish

⬜ **Onboarding dots-count (recap 7 vs add-to-home 6)** · **The stacked-Pip-voice line on the first setup screen** (yellow "Ember's still warm" sits under a header that now also speaks — cosmetic) · **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **STALE chip predicate** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

*(Removed from polish: "Pip install tutorial" — SHIPPED 2026-07-29.)*

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat: auto-default vs tappable "pick yours" (NEW, unratified)** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B — no longer Forge-driven)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **STALE LOCAL BITS MIMIC A MISSING FEATURE. (NEW — 2026-07-29.)** The install screen "wasn't showing" because the test device sat on an old commit (`369164e`); the pull to `d9b8356` had already added it. Not a bug — stale state. **Verify the deployed/pulled commit before diagnosing a missing feature.**
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE. (NEW — 2026-07-29.)** A three-tap install does not need a nine-screen state machine. Out-habit, don't out-feature — applied to onboarding itself. The bloat instinct wears a thoroughness costume.
- **UNBUNDLE WELDED ASSUMPTIONS. (NEW — 2026-07-29.)** "Service worker = installable PWA" was two jobs fused in a doc. Install needs manifest + apple-tags; the SW is offline resilience. Fusing them would have delayed a shipped feature behind an unrelated one that iOS users never benefit from.
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT. (NEW — 2026-07-29.)** `handoff` looked like a redundant screen; it carried the terminal navigation that reached the install tutorial. A blind delete severs the chain. The free recon caught it.
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

- **2026-07-29** — the install tutorial. Manifest launch-polish (`background_color`, iOS status-bar style); Add-to-Home-Screen step as terminal creator first-run screen (real share-sheet screenshot, forge-window border, platform-conditional copy, webview escape, suppress-if-installed); `handoff` ("Now show them") cut with terminal wiring transplanted to `recap`; first-screen copy fixed; replay-intro handler fixed; first-quest doorway confirmed built. Verified in preview; prod walk owed.
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

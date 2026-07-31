# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-30 (late)** — *the QA/design session.* **Zero code. Zero credits. Six decisions, three of them structural.** May filed three QA items from live use of the product; one of them turned out to be an already-carried 🔴 that nobody had connected to a user complaint, and one of them killed a fix jAIne had proposed an hour earlier.

**The headline: "Quest" is dead. "Bounty" is the universal object term, and Quest Log is replaced by two surfaces — the Slate and the Ledger.**

**The circling was a symptom of the rule, not of an unmade decision.** Quest-vs-Bounty had been LOCKED twice (07-04 establishing "Quest universal, Bounty proper-noun-only"; 07-11 enforcing it by banning the card-level chip) and it kept resurfacing anyway. The diagnosis: **the rule permitted both words on screen simultaneously, in different jobs.** That is a rule a designer can hold in his head and a user structurally cannot — May sees two nouns for one object, and so does every kid and every stranger. Re-litigation was the correct response to an incoherent rule. **The fix was not picking a winner; it was killing the dual-vocabulary carve-out.**

**The Slate came out of a Scott correction that reframed the whole item.** jAIne diagnosed May's duplication complaint correctly (there is no object representing a standing duty — only instances, chained by approval) and then answered a *findability* question with *migration plumbing*. Scott pushed back on the proposed roll-forward as "messy, greatly reduces functionality" — reacting to mechanics that had been framed as design — and in doing so named the actual gap: **there is no completion state for a cycle. Nothing anywhere says the hold is well-kept.** That is a missing habit loop sitting dead-center of the thesis, and it was not on any list.

**Scott also overruled jAIne on embers, with the better argument.** jAIne excluded ember values from the Slate on membrane grounds ("it becomes a second board"). Wrong frame: jAIne was reasoning about *reading*, Scott about *editing*. Correcting a mis-valued bounty currently costs a delete-and-recreate, which destroys the row's history and, for a recurring duty, breaks the succession chain. **The Slate is the only surface where that correction is coherent.** Embers are in.

**And a jAIne proposal was killed by her own evidence.** She floated a title `maxLength` at creation as the cheap upstream fix for truncation. The photos showed `Wash/Load Dishwasher` (20 chars) and `10 min massage` (14 chars) truncating. **The names are not long; the containers are narrow.** A cap would have punished users for a layout defect.

Last session (prior): **2026-07-30 (early)** — the redemption `decided_by` fix. Shipped and verified on live data.
Last session (prior): **2026-07-29 (late)** — the master-spec fold + the first-run completion marker.
Last session (prior): **2026-07-29 (early)** — the install tutorial.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for EVERY role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a six-step creator first run, eight non-creator first-run screens, and redemption approval that records the adult who actually signed off.

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **⚠️ `master-spec.md` OWES A FULL REGENERATION AND IT IS DELIBERATELY NOT DONE.** The Bounty rename inverts the string law (spec lines 41–42), renames the doc's core object noun throughout 669 lines, replaces the Quest Log surface entry with two new ones, and amends the recurring lifecycle. **This is a rewrite, not a fold, and it must be sequenced AFTER the rename sweep lands** — regenerating canon to say "Bounty" while the live app says "Quest" would make the doc diverge from the product, which is the exact disease the repo exists to prevent. **Until then, the 07-30 LOCKED decisions govern over the spec's older lines.** Dedicated spec session owed.

> **`north-star.md` is current and needs no regeneration.** Thesis, gate ladder and success definition are unchanged. Two strings inherit the rename when the sweep lands: the membrane line ("the game lives in quests only") and the one-line description. Cosmetic inheritance, not a north-star change.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🔴 The marker's read and write, fixed as ONE change.** `FirstRunGate` reads via `getActiveMemberIdSync() ?? auth.uid()`; `mark_first_run_complete()` writes `WHERE id = auth.uid()`. A kid sub-profile's row id is not any `auth.uid()`, so a kid's write **no-ops silently.** ⚠️ **The two bugs cancel — fixing the read alone produces an infinite flow loop.** Needs a validated `profile_id` parameter, family-checked server-side. Migration + frontend, Lovable lane. | The kid joiner flow on shared devices. Gate D. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. Check #1 is closed. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. |
| **4** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

---

## 🟢 DECIDED — 2026-07-30 (late). Nothing shipped; all of this is direction.

### The vocabulary supersede
- ✅ **"Bounty" is now the universal object term.** Supersedes the 07-04 "Quest is universal" decision and moots the 07-11 chip-ban enforcement.
- ✅ **Identifiers do NOT change.** The `quests` table, the `bounty_posted` enum, every column name — all stay. Per the 07-11 identifiers-vs-copy rule, this is a display-string sweep, not a migration.
- ⬜ **THE SWEEP HAS NOT RUN. This is the landing check and it is the whole risk.** A LOCKED rename with no landing is a wish — Feast→Hall sat unshipped for ten days. **The sweep rides on the Slate build prompt, not as a standalone job.**
- ⬜ **One naming sub-question the sweep must answer before it runs:** every surface downstream of the rename. Do not let it ambush the prompt.

### The Slate and the Ledger
- ✅ **The Slate replaces Quest Log.** The Ledger holds history.
- ✅ **The boundary rule: the Slate holds anything with a FUTURE; the Ledger holds anything FINISHED.**
- ✅ **Two sections, because there are two object shapes.** A standing duty has a *next*; a one-off has an *end*. Quest Log's sin is treating them as one list.
- ✅ **Standing duties: one row per duty, forever.** Never leave the Slate — there is always a next cycle. Individual completions go to the Ledger.
- ✅ **One-offs live on the Slate until approved, then move to the Ledger permanently.**
- ✅ **Live / submitted / approved are STATES ON THE ROW for a duty**, not separate rows. For a one-off, approved is what evicts it.
- ✅ **Retired duties stay visible on the Slate**, dimmed and collapsed. `archived` already exists; no new column, no migration.
- ✅ **Embers render on the row and are editable there.** Scott's call, over jAIne's objection.
- ✅ **Brightness is the state dial, reused not invented.** Bright = demands you now; dim = handled. **A dim Slate IS a well-kept hold** — the health read is visual before it is numeric.
- ✅ **No claim action on the Slate.** Claiming happens on the board. The Slate reads and edits; it does not play.
- ⚠️ **Two brightness signals will coexist on one row** — the ember value's tier ramp (Dim→Blazing) and the row's state heat. Not a conflict; the board already runs both on one card. **But the health read comes from the ROW, not the number: a Blazing 500-ember duty that is current must still sit dim.** Pinned so nobody "fixes" it later.

### The roll-forward rule
- ✅ **`open` and `claimed` recurring instances roll FORWARD (same row, new anchor). `submitted` does NOT.** A submitted bounty sits in the *adult's* queue; rolling it punishes the kid for the adult's lag.
- ✅ **Roll-forward is the PRECONDITION for the Slate, not a companion fix.** One row per duty only holds if the row moves. Spawn-on-approval without it produces three stranded trash rows by August and an unreadable Slate.
- ✅ **Claimed rows keep their claim on roll**, consistent with the 07-21 lifecycle decision (assignment is permanent; only the cycle resets).

### The truncation class
- ✅ **Diagnosed as ONE class: the title is the only element permitted to shrink.** Confirmed present on Vault manage rows, the wall's Claimable list, and the wall's Recently Completed list.
- ✅ **The fix is a shared row primitive** — shrink-to-fit first, then wrap, capped at two lines; badge drops below the title rather than beside it.
- ✅ **Marquee DECLINED.** Six simultaneously scrolling titles on a wall is a slot machine. Motion was requested because truncation felt broken; the fix is to stop truncating.
- ✅ **Title `maxLength` DECLINED.** jAIne's proposal, killed by the photos.
- ✅ **The wall's mid-row clip is NOT a defect.** Scott's call, from actual daily use.
- ⬜ **The badge-is-the-culprit diagnosis is a HYPOTHESIS FROM PIXELS, not from code.** Three-for-three / six-for-six correlation in a screenshot. **The recon below is briefed to disprove it.**

---

## ⬜ OPEN — the next work, in order

- ⬜ **🔴 THE ROW-PRIMITIVE RECON — read-only, zero credits, Sonnet. RUN THIS FIRST.** Four hypotheses (badge-is-culprit · N independent implementations · title-always-loses · length-is-not-the-variable) plus a recurring-quest census by cadence. **Briefed to DISPROVE.** Blocks the row-primitive build and sizes the Slate's collapse behavior.
- ⬜ **🔴 THE SHARED ROW PRIMITIVE — Claude Code, zero credits, auto-accept ON.** Frontend only, eyeball-verifiable, one commit, `git revert`-able. ⚠️ **Wider blast radius than a one-surface patch** — a shared primitive touches Vault, wall and board at once. Scott eyeballs three surfaces, not one. **Isolated push, no stacking.**
- ⬜ **🔴 THE SLATE + THE LEDGER — Lovable lane. The next credit spend.** Carries the roll-forward migration AND the Bounty sweep. Do not un-bundle the sweep from it.
- ⬜ **🔴 The marker read/write pair.** CRITICAL PATH #1, unchanged.
- ⬜ **The recurrence chip must read `Monthly · 1st` / `Weekly · Mon`.** The anchor arithmetic discards the user's chosen day and nothing in the UI says so. **Now higher-value than it was** — the Slate surfaces "next due" on every row, so a lying chip becomes a lying Slate.
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by the roll-forward fix** — verify before building anything separate.

---

## 🟡 PENDING VERIFY

- 🟡 **The Vault-page approval path is unverified post-migration.** Single adult, approve a pending kid redemption from the Vault page — should behave exactly as before via the NULL default. **Cheap, do it next session.**
- 🟡 **The kid joiner flow has never been walked by a kid.** **Blocked in practice on the shared-device path by critical-path #1.**
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.** **Phaeaz's next login is the natural test.**
- ⚠️ **Wall adult-verified turn-in (quests).** Verify: approve → PIN → wrong/kid PIN mints nothing → correct PIN commits.
- 🟡 **Monthly post-fix is technically unexercised.** ⚠️ **August 1 is the natural test and it is two days out** — every monthly successor is dated 2026-08-01 and they all land at once. **Watch that board Saturday morning.**
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED FIVE TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS. *Sonnet · auto-accept OFF · read-only.*
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** — visual success criterion, Scott only.
- 🟡 **Avatar render fallback ("the floor")** — **check the wall specifically.**
- 🟡 **Founder tier-tag verification.** Flip gate ON, confirm **32 lock / 16 open**, flip back OFF.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, back in, confirm auto-advance on admission.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not exercised across a full session. **Now also the reuse candidate for board clutter under roll-forward.**
- 🟡 **Phaeaz cold-account retest** — now the test case for the timezone heal.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device.

---

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

- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by` now is.** `enforce_quest_update_authority` checks the *session owner's* role; `enforce_quest_family_refs` checks only same-family membership. **Neither checks that the written value names an adult.** Not urgent — intra-household under the walk-up boundary — but redemption now has the pattern to sweep toward.
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. ⚠️ **They are not interchangeable.** `has_role()` is only valid for auth-backed identities; `profiles.role` is authoritative for an arbitrary profile id.
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **`routeTree.gen.ts` was hand-edited, toolchain drift underneath.**
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Quest creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.** **Feed verb drift.**
- ⬜ ~~**Quest Log applies no `due_date` filter — by design or by omission?**~~ **DISSOLVED.** Quest Log is being replaced. The question was always "what is this surface for," and the Slate answers it.

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
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage` (closed by deletion).

**Real, open:**
- ⬜ **`quests.approved_by` accepts any in-family profile id, including a kid's.**
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
- ⬜ **Prod test-object cleanup — deserves its own session.** Includes the *"Testing redemption tracking"* quest, the 07-30 verification reward, and the test holds from the 07-29 glass checks.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The marker read/write pair.** CRITICAL PATH #1.
- ⬜ **`master-spec.md` full regeneration, sequenced after the Bounty sweep.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The stacked-Pip-voice line on the first setup screen** · **`points` surfacing as a user-facing noun** on `/first-run/adult/approving` · **Feed verb drift** · **`member_admitted` feed line** · **Recurrence chip legibility** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat: auto-default vs tappable "pick yours"** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

**⚠️ Sharpened 2026-07-30:** the boundary is why `quests.approved_by` accepting any in-family profile id is tolerable rather than critical. It is not why it is *correct*.

---

## 🔵 THE BUILD MODEL — holding

- **A RULE THAT PERMITS TWO WORDS FOR ONE OBJECT IS NOT A DECISION. (NEW — 2026-07-30 late.)** Quest-vs-Bounty was LOCKED twice and kept resurfacing, and the resurfacing was *correct*. The 07-04 rule carved out a proper-noun exception, which a designer can hold and a user cannot. **When the same question keeps coming back after a LOCKED call, suspect the rule before suspecting the discipline.**
- **ANSWER THE QUESTION THAT WAS ASKED. (NEW — 2026-07-30 late.)** May's QA item was *"I can't tell if this already exists"* — a findability complaint. jAIne led with migration mechanics and buried the surface fix in a subordinate clause. **Scott then reacted to the plumbing as if it were the design, reasonably, because it had been framed that way.** The register of the answer has to match the register of the question.
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS. (NEW — 2026-07-30 late.)** jAIne asserted the ADULTS ONLY badge was the truncation culprit on a three-for-three / six-for-six pattern in a photo. That is pixel evidence, not code evidence, and it is now briefed to disprove. **State it as a hypothesis before the recon runs, not after.**
- **FIX THE CONTAINER, NOT THE CONTENT. (NEW — 2026-07-30 late.)** jAIne proposed a title `maxLength` as the cheap upstream fix. The truncating titles were 14 and 20 characters. **The proposal would have made users pay for a layout defect.** The tell: the "cheap upstream fix" constrains the user rather than the code.
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS. (NEW — 2026-07-30 late.)** Scott's call. `Video Game` is Title Case among sentence-case siblings because a human typed it. The string law governs *app copy*. **An audit that "fixes" a household's own typing is a category error, and a future sweep will absolutely try.**
- **REVERTIBILITY IS A LANE CRITERION, NOT JUST CREDITS. (NEW — 2026-07-30 late.)** Scott asked for the row fix as its own Lovable push so it could be reverted easily. The real requirement was *isolated and trivially revertible*, which Code serves better — one commit, `git revert`, zero credits. **Read the requirement, not the named tool.**
- **A 🔴 WITH NO CONSUMER IS NOT A 🔴.** Before writing a prompt for a carried 🔴, ask what reads it.
- **THE FIX WAS RIGHT AND THE REASON WAS WRONG.** Record the real reason; it stops the next session re-arguing from the old one.
- **A PRESCRIPTION IN THE SPEC IS STILL A HYPOTHESIS.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** `create_adult_profile` mints profiles with no `auth.users` row.
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.** Drop, recreate, re-grant.
- **WHEN THERE IS NO GLASS, VERIFY THE DATA.**
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.** Divergence announces itself; cancellation hides.
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX — OR THE ONE THE FIX DEPENDS ON.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.** Report the hashes first, before reading.
- **STALE LOCAL BITS MIMIC A MISSING FEATURE.**
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.**
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

- **2026-07-30 (late)** — QA/design session. Zero code. Bounty supersedes Quest; the Slate and the Ledger replace Quest Log; the roll-forward rule decided; the truncation class diagnosed; three proposals declined (two of them jAIne's).
- **2026-07-30 (early)** — the redemption `decided_by` fix. `approve_redemption` + `deny_redemption` re-signed with a validated optional approver id, old signatures dropped and re-granted; `wall.tsx` stops discarding the PIN-verified adult. Verified on live data.
- **2026-07-29 (late)** — the master-spec fold + the first-run completion marker + the arrival-gate exemption fix (`943a633`) + the onboarding dots-count fix.
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

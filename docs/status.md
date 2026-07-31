# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-30 (night)** — *the Slate + Ledger build.* **Five Lovable credits, one free Code job, and the biggest single surface change since the wall.** Quest Log is replaced. The roll-forward rule is built and half-verified. The Bounty rename landed on the glass.

**The headline: the Slate and the Ledger are live, `roll_missed_dailies` moves the same row forward for all three cadences, and "Quest" is off the product.**

**Two canon facts were corrected by live data, both against the docs.** *"Take out the trash"* was recorded as **submitted, due 07-21, stranded**. It is `approved`/`archived` with a future-dated successor at 2026-08-03 — the Ledger renders its approval on Monday Jul 27. And there was **one** stranded past-due row, not two. **The guilt-pile argument was built on an empirical base half the size canon claimed.** The rule still stands on its own merits — unbounded accumulation is wrong whether or not it had happened yet — but the recon that produced both errors has not been audited and its other claims now carry less weight than they did.

**A monthly false alarm, corrected by Scott.** jAIne read three "Due now" monthlies as evidence the monthly branch had dragged future successors backward. They were recently created and had never needed to roll. **Reaching for a defect before the simpler explanation, twice in one night against canon, is the pattern worth naming.**

**Scott was right to send it and jAIne was wrong to recommend holding.** jAIne sized iteration risk off bundle width. **Lovable fails by stopping, not by half-landing** — which inverts the heuristic entirely. When a tool fails safe, width stops predicting spend; ambiguity does. This bundle was wide and unambiguous, which is the cheap quadrant.

**Code costs zero Lovable credits — confirmed empirically, not assumed.** Measured before and after a full Code job with a repo push and a Lovable re-sync. The lane-routing rule that depends on this holds.

Last session (prior): **2026-07-30 (late)** — the QA/design session. Bounty supersedes Quest; the Slate and the Ledger; the roll-forward rule.
Last session (prior): **2026-07-30 (early)** — the redemption `decided_by` fix.
Last session (prior): **2026-07-29 (late)** — the master-spec fold + the first-run completion marker.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, with a working activation path for EVERY role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, a tenant-isolation model verified under live authenticated attack, clean function and table grant surfaces, a six-step creator first run, eight non-creator first-run screens, redemption approval that records the adult who signed off — **and, as of tonight, the Slate, the Ledger, and a rollover engine that moves rows instead of spawning them.**

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> **⚠️ `master-spec.md` OWES A FULL REGENERATION AND IT IS NOW UNBLOCKED.** The sequencing condition — *"after the rename sweep lands"* — **is met.** The glass says Bounty. The spec's Quest Log surface entry describes a retired surface, its Slate section carries a two-brightness-signal warning that tonight superseded, its recurring-lifecycle section describes roll-forward as decided-not-built, and the navigation section's open question is answered. **jAIne did not regenerate it at close-out and said so rather than faking it** — she read roughly 200 of 669 lines this session. **Dedicated spec session, first in the queue. Until then the 07-30 LOCKED decisions govern over the spec's older lines.**

> **`north-star.md` is current and needs no regeneration.** Thesis, gate ladder and success definition unchanged. Two strings inherit the rename cosmetically.

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

## ✅ SHIPPED TONIGHT — 2026-07-30 (night)

### Part A — the rollover migration *(Lovable, live DB)*
- ✅ **`roll_missed_dailies` moves the SAME ROW forward for all three cadences.** Daily → `household_today()`; weekly → Monday of the current week; monthly → the 1st of the current month. **No archive-and-spawn.** The prior implementation was the declined one and was replaced, not extended.
- ✅ **`submitted` rows are excluded.** `claimed_by` survives the roll.
- ✅ **`quests.retired_at TIMESTAMPTZ` added, nullable, no backfill.** SQL COMMENTs on both objects.
- ✅ **VERIFIED ON THE GLASS: *Grocery Shopping* was due 2026-07-06, claimed by May. It is now due and still claimed by May.** Same row, moved, claim intact. **This is Part A's core guarantee and it is closed.**
- 🟡 **The `submitted` exclusion is UNEXERCISED.** There are no submitted rows in the hold. Built, not proven.
- 🟡 **The MONTHLY roll branch is UNEXERCISED and August 1 will not exercise it.** Aug 1 tests *successor arithmetic*. Roll-forward fires only when a monthly sits unapproved **past** its anchor — **no natural test until September 1.** Do not let this be logged as verified.

### Part B — the Slate *(`/slate`)*
- ✅ Reads past the `due_date` gate deliberately; uses the shared `isActiveQuest` predicate.
- ✅ Sections: **One-offs · Standing · Retired.** All/Daily/Weekly/Monthly filter. `"N of M current · K need doing"` header. Secondary Ledger link.
- ✅ **Brightness is state-only.** Collapse is state-driven — `"N more current"`, no numeric threshold.
- ✅ **Quest Log's full detail panel migrated intact**, including **Reject** — an affordance jAIne had never seen and never named. *The "preserve what isn't enumerated" instruction earned its place.*
- ✅ **Retire replaces Delete on ever-approved bounties**; Delete survives on never-approved.
- ✅ Pip empty state → `/create?recurring=true`.
- ✅ **One-offs above Standing under All; hidden entirely under a cadence filter** *(Code, follow-up commit `e186ff0`)*. The `"Filtered out by cadence."` empty state is deleted — a one-off has no cadence, so the section is inapplicable, not empty.

### Part C — the Ledger *(`/ledger`)*
- ✅ Reads `quests WHERE status='approved'`, **not `activity_log`** — that table's verb drift and `actor_label` ambiguity are not inherited.
- ✅ Reverse-chronological, day headers, **read-only**, no heat. Member filter defaulting to self for kids, the hold for adults.

### Part D — the Bounty sweep
- ✅ **Ran file-by-file with a justification table**, `${obj}` interpolations left alone, identifiers and route files untouched, user-authored content untouched — **"Vaccuum Downstairs" is still misspelled, which is correct.**
- ✅ Both flagged `/first-run/adult/approving` violations fixed in one string.
- ⚠️ **The sweep reported `quest.$id.tsx` swept having changed only the head title. Code later found TWO user-facing strings still in it** — *"Delete quest"* (named in the brief) and *"Claim this quest"* (not named, found by reading). **A file-by-file sweep still under-read a file it visited.**
- ⬜ **A repo-wide grep for user-facing `quest` strings is OWED before the rename is called landed.** Free, Code lane. **The 07-11 precedent is exactly a sweep that self-reported clean.**

### Part E — entry points
- ✅ Briefing: Quest Log card and **Hearth Log debug card** removed; Slate card added.
- ✅ **`/quest-log` and `/hearth-log` left routable and unlinked** — the rollback path. Their own copy is deliberately unswept so it stays obvious which surface is which.
- ⬜ **Route deletion is a follow-up**, after the Slate is trusted.

### Typography — resolved without the primitive
- ✅ **NOT A SINGLE TITLE TRUNCATED ON THE SLATE.** *"Change sheets in Master Bedroom"* wrapped to two lines in the densest row in the product. **Scott didn't think to look until afterward — which is the verification. Nobody notices correct typography.**

---

## ⬜ OPEN — the next work, in order

- ⬜ **🔴 THE MARKER READ/WRITE PAIR.** Critical path #1. Lovable lane, needs credits.
- ⬜ **🔴 `master-spec.md` FULL REGENERATION — now unblocked, dedicated session.** Six of tonight's decisions override its current lines.
- ⬜ **🔴 REPO-WIDE SWEEP-COVERAGE GREP.** Free, Code. The rename is not landed until it runs.
- ⬜ **🟠 THE ROW PRIMITIVE — RE-SCOPED, SMALLER.** ⚠️ **The job is probably DELETING a class at three call sites, not building a component and threading it through eighteen.** The board's `QuestCard` already wraps and never truncates; the Slate now does the same; two independently-authored surfaces are both fine. **The defect is three sites that declared `truncate` explicitly, not an absent shared component.** ⚠️ **The wall is the exception and it is Scott's eyeball** — it is a fixed-height ambient rail, so wrapping there may push rows out of view. Cap-at-two-lines earns its keep there and possibly only there.
- ⬜ **Delete `/quest-log` and `/hearth-log` routes** once the Slate is trusted.
- ⬜ **The recurrence chip already reads `Monthly · 1st` / `Weekly · Mon` on the Slate.** Confirm it reads the same on the board and in create/edit, or the surfaces disagree.
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building anything separate.**
- ⬜ **The Briefing now makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card advertising "standing duties & open bounties." Cosmetic, but two doors to overlapping content.
- ⬜ **`Testing redemption tracking` is now visible in a user-facing history surface.** Prod test-object cleanup gained a reason to happen sooner.

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE RETIRE PATH HAS NEVER BEEN RUN.** Retire a reversible duty → confirm it lands dim in Retired, Delete is gone, and it is filtered on `retired_at` and not `archived`.
- 🟡 **🔴 `submitted` DOES NOT ROLL — no test case exists.** Turn in a bounty, do not approve it, mount the board, confirm it did not move. **Free.**
- 🟡 **🔴 THE MONTHLY ROLL BRANCH — no natural test until 2026-09-01.**
- 🟡 **`/create?recurring=true`** — a query param that did not exist this morning. Tap the Slate's empty state on a test hold and confirm it opens with a cadence on.
- 🟡 **Feed verb history.** If old activity rows stored their verb string rather than rendering from the enum, history says "quest" while new entries say "bounty" — **mixed vocabulary, the exact disease the rename killed.** Scroll the feed.
- 🟡 **AUGUST 1 — THE MONTHLY SUCCESSOR ARITHMETIC.** Every monthly successor is dated 2026-08-01 and they land the same morning. **This tests spawn anchoring and board clutter. It does NOT test roll-forward.** Costs nothing but looking.
- 🟡 **`13 need doing` on a real hold.** Roll-forward moved every stale weekly to the current anchor, so they all read "Due now" at once. Correct — one row per duty, no pile — but **it is the first look at what the rule produces, and the Slate is bright-heavy today.** Honest or oppressive is a taste call. **Watch it over a week before touching it.**
- 🟡 **The Vault-page approval path post-migration.** Single adult, approve a pending kid redemption from the Vault page. **Cheap.**
- 🟡 **The kid joiner flow has never been walked by a kid.** Blocked on the shared-device path by critical-path #1.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in.** approve → PIN → wrong/kid PIN mints nothing → correct PIN commits.
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED SIX TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS.
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** · **Avatar render fallback — check the wall** · **Founder tier-tag verification (32 lock / 16 open)** · **Routing fix `28ab40d`** · **Lists collapsible sections `fb6aa99`** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Wall full end-to-end membrane loop.**

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Claude Code job: pure TypeScript, ten tests, zero Supabase imports, zero credits.** *(Note: `tsc --noEmit` currently shows one pre-existing error — `vitest` types missing in `progression.test.ts`. Unrelated to tonight, but it is in this file's neighborhood.)*
- ⬜ **The engine cannot accept a pre-session constraint.** Scope with the rep-compensation job.
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.**
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by` now is.** Neither trigger checks the written value names an adult. **Tolerable under the walk-up boundary, not correct.** Redemption now has the pattern to sweep toward.
- ⬜ **Two derivations of role now exist.** `FirstRunGate` reads `profiles.role`; `useMyProfile()` derives from `user_roles`. ⚠️ **Not interchangeable.**
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **⚠️ `routeTree.gen.ts` drift is CONFIRMED LIVE, not suspected.** The build regenerated it with boilerplate; Code discarded it to keep the commit scoped. **Correct call — but the generated file and the committed file disagree, every build surfaces it, and every agent has to know to throw it away. Sooner or later one won't.**
- ⬜ **`member_admitted` renders as `"Mom · Leo"`.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.**
- ⬜ **Ghost successor cleanup.** **Feed verb drift.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.**
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

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ⬜ **Service worker + app-shell cache.** Gate B. **Deserves its own careful pass.**
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup — now visible to users in the Ledger.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **The marker read/write pair.** CRITICAL PATH #1.
- ⬜ **`master-spec.md` full regeneration — unblocked, dedicated session.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The stacked-Pip-voice line on the first setup screen** · **Feed verb drift** · **`member_admitted` feed line** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model · **the service worker (offline shell, Gate B)**.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

---

## 🔵 THE BUILD MODEL — holding

- **WHEN A TOOL FAILS SAFE, WIDTH STOPS PREDICTING SPEND. (NEW — 2026-07-30 night.)** jAIne recommended holding a five-part bundle on five credits, sizing iteration risk off how much it carried. **Lovable stops rather than half-landing** — so the expensive prompt is the *ambiguous* one, not the wide one. Wide-and-unambiguous is the cheap quadrant. **Scott had the operational fact; jAIne had the model. The model was wrong without it.**
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT. (NEW — 2026-07-30 night.)** jAIne read three "Due now" monthlies as a rollover defect dragging future rows backward. They were newly created. **Second time in one night that canon's version of events lost to live data — and the first two corrections should have lowered confidence in the third reading, not left it unchanged.**
- **A FILE-BY-FILE SWEEP CAN STILL UNDER-READ A FILE IT VISITED. (NEW — 2026-07-30 night.)** The sweep table listed `quest.$id.tsx` as swept and had changed only the head title. Two user-facing strings remained, one of which nobody had named. **A justification table is better evidence than a self-report and is still not coverage. Grep after sweeping.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED. (NEW — 2026-07-30 night.)** Checked before and after a Code job that pushed and re-synced. **The whole lane-routing rule rests on this; it is now evidence.**
- **A RULE THAT PERMITS TWO WORDS FOR ONE OBJECT IS NOT A DECISION.**
- **ANSWER THE QUESTION THAT WAS ASKED.**
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS.** ⚠️ **Confirmed the hard way:** the ADULTS ONLY badge hypothesis failed on read. `truncate` is applied unconditionally; `overflow-hidden` zeroes the title's flex auto-min-width, so it absorbs the squeeze **by CSS default, not by anyone's declaration.**
- **FIX THE CONTAINER, NOT THE CONTENT.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.** *("Vaccuum Downstairs" survived the sweep. Correct.)*
- **REVERTIBILITY IS A LANE CRITERION, NOT JUST CREDITS.**
- **A 🔴 WITH NO CONSUMER IS NOT A 🔴.**
- **THE FIX WAS RIGHT AND THE REASON WAS WRONG.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.**
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.**
- **WHEN THERE IS NO GLASS, VERIFY THE DATA.**
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.**
- **A GUARD WRITTEN FOR ONE AUDIENCE WILL MEET ANOTHER.**
- **DON'T FENCE THE FILE THAT HOLDS THE FIX.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.** *(Worked tonight — Code reported being behind, pulled, and reported the hash before reading.)*
- **RIGHT-SIZE THE GUIDANCE TO THE GESTURE.** · **UNBUNDLE WELDED ASSUMPTIONS.**
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.** *(This is why `/quest-log` is still routable.)*
- **PRESERVE WHAT ISN'T ENUMERATED.** *(Reject survived a panel migration because the brief said carry everything, not carry this list.)*
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.**
- **RECON CAN KILL YOUR RECOMMENDATION, AND THAT IS THE POINT.**
- **PLAIN-SPEAK THE PROBLEM BEFORE BUILDING THE FIX.** · **SEVERITY IN A DOC OUTLIVES THE EVIDENCE FOR IT.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.** · **A prompt's DO-NOT-BUILD list is not self-enforcing.**
- **A code revert is not a database revert.** Undo schema forward, always.
- **The docs are not the live codebase — and can diverge silently.** ⚠️ **Twice tonight, in the docs' direction.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **Recon before build, every time. Brief recon to DISPROVE, not to confirm.**
- **HTTP 200 is not "renders."** **A preview is not prod.**
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis + synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.
- **Lovable does not always honor prompt ordering.**
- **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-07-30 (night)** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. Quest Log and the Hearth Log debug card off the Briefing. The Bounty display-string sweep landed. One-offs reordered, `quest.$id.tsx` swept (Code, `e186ff0`). Five credits.
- **2026-07-30 (late)** — QA/design session. Zero code. Bounty supersedes Quest; the Slate and the Ledger; the roll-forward rule; the truncation class; three proposals declined.
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

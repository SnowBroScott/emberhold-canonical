# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**

## How this works
- **Inbox** — the dump zone. Capture and move on. Don't categorize, don't fix.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them. *Capturing and deciding are different jobs.*
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-16)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Staging / dev database — do we need one before beta?** *(new 2026-07-15)* Local dev points at the **same Supabase backend as production** (theemberhold.com). Confirmed the hard way: Code created live test accounts (`QA Parent`/`QA Joiner`/household `QA Verify Hold`) on the prod DB while exercising the admit flow, because there's nowhere else for them to go. This means every local-dev test writes to the same tables as thirteen real users' data. Fine at Alpha; a real Gate-B concern once strangers are on it — test data, destructive test actions, and adversarial-audit writes all land in prod. *Does a separate staging/dev DB (or at least a seed/teardown convention) come before distribution?* Related to the P4×L8 harness-reachability question below.
- **⚠️ Backend ownership + data backup.** The backend is **Lovable Cloud**, not a Supabase project Scott owns. There is no separate dashboard, and there is **no direct Supabase access** — everything backend routes through Lovable. **Backups, plan tier, PITR, data export, and the ability to leave are all Lovable's to grant.** The code has a Git backup; **the data has none.** A live structural risk and a Gate B blocker. *What is the exit path if Lovable Cloud is the wrong long-term home?*
- **Does the P4×L8 adversarial script even run on Lovable Cloud?** The harness assumes a project URL + anon key pointable at the DB from outside the app. `supabase-js` is initialized *somewhere* in the repo — check there first. If those credentials aren't usable externally, the audit degrades to policy-reading and Gate B's exit criterion (*"cross-tenant access provably impossible"*) becomes unprovable by design. **Answer this before running the audit.** *(2026-07-16: the Data-API grant surface is now a named input to this audit — see `decisions.md`.)*
- **⚠️ The free/paid avatar split — and its arithmetic is now stale.** "16 free / 24 paid" was locked against a **44**-character roster. The roster is **48**. The math no longer closes, the selection was never made, and the old split predates a cast nobody has looked at yet. **Re-lock the numbers, then cast.** *This is a product decision and must never be handed to an agent as part of avatar transport.*
- **What happens to the thirteen existing `hero:` picks.** A roster swap silently turns every current member into a different character unless stale ids are invalidated. **Direction is clear** (invalidate + re-choose, framed as a re-forge). **Open:** does re-choosing get a proper moment — a Pip-voiced prompt, a one-time card — or just a reset to the default emoji? *The moment is nearly free and it's a re-engagement beat for thirteen people who have already drifted.*
- **Quality — the two open halves.** Direction LOCKED (a signal, never an ember modifier). Still open: (1) is it visible to the KID, or adult-only? (2) What consumes it — the weekly recap, presumably, which is parked behind beta.
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks — and is about to speak a lot more in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder** — simultaneously "the unproven core bet" and "fenced to post-launch." Both defensible; they can't both be true forever. **Note (2026-07-15): it's also the one delight item that doesn't need the security gate open** — it's for Scott's own household, not strangers — which makes it the defensible morale pull-forward if a fun session is wanted before P4×L8. See `north-star.md`.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags today, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **Hand-cut the 48-avatar roster in GIMP.** Ellipse-select, fixed 1:1, snapped to the gold rim and **biased 2–3px inward.** Losing a sliver of rim is free; keeping a pixel of a neighbour is not. *Automation was attempted four ways and abandoned — see `status.md`.* **TABLED by Scott — on the board, not a priority; surface it, don't push it.**
- **Cast while cutting.** The roster is being cut from ~57 generated portraits into 48 slots. **The casting decision happens while Scott is looking at them, because that's the only time it can happen.** Whatever doesn't make the cut is the surplus, and the surplus is already paid for.

---

## NEXT (soon — off the critical path)

- **`avatar-review.tsx` — retire or rebuild.** A leftover review page that hand-duplicates the roster in its own local array. It is a second source of truth for the cast, and it will drift. *Kill it unless it's earning something.*
- **Audit what else from prior sessions never landed.** `Feast` → `Hall` was LOCKED and sat unshipped for ten days, found by accident. **2026-07-15 reinforced this hard:** three separate 07-14 "shipped" claims turned out to be design-complete-not-landed (the admit/deny enum bug, the reconcile escalation, and the `recurrence_day` frontend removal that was *stashed, not committed* — and crashed quest creation in prod). See the LOCKED decision "07-14 batch was logged from INTENT, not landed code." **Grep every LOCKED decision against the codebase.** *Haiku job, cheap, and now overdue.*
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Recurrence now anchors to fixed dates (weekly=Monday, monthly=the 1st), but the anchors are invisible to users — a kid sees a bare "MONTHLY" chip with no hint it means the 1st. `RECURRENCE_LABEL` is a plain constant; surfacing the anchor needs conditional render at the chip sites (`QuestCard`, `quest.$id`, `quest-log`). Scoped and skipped during the recurrence cleanup because it exceeds a one-line change. *Cheap, and exactly the ambient legibility the thesis runs on.*
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model, and favoriting is load-bearing.
- **Quick Add defaults expanded on an empty board** — zero quests means the user has never seen one. Same state-driven pattern as the Vault's catalog/curated flip.
- **A cheap Dim-tier starter reward** — the menu floors at 25, so a new kid can't cash out until they've earned 25. **Doubly important now:** the Pip first-run screen asks them to stock the Vault, and the first reward they set should be *reachable*, not a 500-ember Urban Air Day.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · Quick Picks dropdown · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · **feed verb drift** (a newly-posted quest still shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED" — a surface that bundle didn't reach).

---

## LATER (backlog)

- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** *(new 2026-07-16)* The role picker is the one identity-first, literal word in an otherwise entirely function-first, themed vocabulary (embers, hearth, hold, quests, Vault). It's the odd one out on both axes at once — it names *who you are* instead of *what you do*, and it does so in flat English instead of the world's voice. It ages badly because it breaks the rule the rest of the app follows. The pigeonholing word is specifically **"Kid"** — "Adult" generalizes fine (a childless couple both pick Adult, the app already handles it clean), but a frat pledge, a guild member, or a roommate who owes dishes is not a "Kid," and the waiting screen literally telling a 24-year-old *"you asked to join as a kid"* is the moment the frame snaps. The real distinction under the hood is **approver vs. submitter** — an authority model the app already implements correctly; "Adult/Kid" is only the skin on it. There is almost certainly a themed, age-neutral pair that's function-accurate AND finally on-brand — the same move that turned "chores" into "quests" — but naming it is its own deliberate session, NOT a drive-by. **Cost honesty:** this is a display-string sweep, not a data migration — the `parent/kid` enum can stay under the hood while the surfaces change. Low technical risk, but WIDE surface (onboarding, admit-as, waiting screen, permission explainer, badges, likely Vault audience copy), and 13 people have the current model in muscle memory. Do it once, deliberately, ideally bundled with another vocab pass. Fine for v1/beta exactly as-is. **Directly adjacent to "Flat / peer holds" below — same "not just families" thesis; this is the labels half, that's the permission-model half.**
- **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + the feed spine (built) + the engine loop-fixes (shipped). Also carries the optional kid picture-lock. *The regenerated roster at ~600px per character finally makes this visually viable; the old 170px assets did not.* **The one delight item not gated behind P4×L8** — see OPEN DECISIONS.
- **#8b — admin/reporting surface** — *parked behind the beta.* Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Six unrelated things sharing a room. **Note: quality's consumer lives in here, which means quality can't ship until at least part of this does.**
- **Weekly recap** — the first real consumer of the activity feed. **Also the consumer of quality.**
- **Other feed consumers** (reads off `activity_log`) — full in-app feed view, richer notification layer, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the legitimate long-term answer to "I want it to be *me*." Pushes an uploaded face *into* the style floor. Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** ~57 portraits were generated for 48 slots, and the count is now concrete rather than theoretical. The overflow is a Guildhall tollbooth item that **already exists and costs nothing further to produce.** *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable (complexity lives in Apple review, not code). Hinges entirely on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model. A chore list earns embers, but it's *built as a multi-step quest*, so the game still lives in quests.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. The neutral vocabulary already leaves room; the permission model doesn't. *Same "not just families" thesis as the role-label retirement above — that's the labels half, this is the permission-model half.*
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED

**2026-07-16**
- ~~**Pending members bounced onto the setup form on re-login**~~ — **FIXED (`28ab40d`).** `signIn()` in `auth.tsx` couldn't tell "no profile yet" from "pending admission" (a pending member's own row is RLS-hidden from themselves), so it fell straight to `finish-setup` on every re-login; combined with a push-not-replace redirect it produced the `[waiting] → [setup]` history stack. Now checks `get_pending_membership()` before falling to setup, guards the setup route directly, redirects use `replace`, and the waiting screen polls + auto-advances on admission. *🟡 pending Scott's live repro — see `status.md`.*
- ~~**Roster / switch-picker "no members" bug**~~ — **FIXED (Lovable, live).** Root cause was NOT code and NOT `b31c92c` — the `public` tables had zero Data-API grants for `authenticated`; every signed-in query failed at the permission layer before RLS. Grants restored across all 14 tables; RLS still gates rows. Fourth live-schema-drift instance. See `decisions.md`.
- ~~**"All quiet at the hold" as an empty-board eulogy bug**~~ — **RESOLVED as working-as-intended.** The line reports the adult's action queue ("nothing needs your seal"), not household inactivity. See `decisions.md`. *(The separate new-household doorway gap is unaffected — LOCKED 2026-07-12, gated behind distribution.)*

**2026-07-15**
- ~~**`recurrence_day` frontend references**~~ — **REMOVED (`172a07f`).** The 07-14 DB drop finally matched by the frontend removal that had been stashed, not committed. Was crashing quest creation in prod.
- ~~**Pending-member roster leak / phantom switch target**~~ — **FIXED (`b31c92c`).** `useFamilyMembers` now filters `status='active'`. *(2026-07-16 footnote: this fix was innocent of the later roster-empty bug — active rows pass an active filter. The real culprit was the grant hole above.)*
- ~~**Admit/deny enum crash (`'notable'`)**~~ — **FIXED.** Changed to `'milestone'` in both RPCs.
- ~~**Walk-up profile-switch as a possible escalation**~~ — **RESOLVED as a deliberate accepted risk** (NOTED in `decisions.md`), not a bug. Client/server agree; the seam is physical-device trust, revisit at P4×L8.

**2026-07-14**
- ~~**`recurrence_day` / monthly day-of-month picker**~~ — **REMOVED.** Monthly is always the 1st; anything bespoke is a calendar event, not a recurring quest. Column dropped, picker gone, trigger rewritten. See `decisions.md`.
- ~~**Join-code policy as an open call**~~ — **DECIDED** as admit-on-approval (lean-on-profiles, data layer shipped). Moved to `decisions.md`. *(Fully shipped + verified 07-15.)*

**2026-07-12**
- ~~**Auth email branding as a "NEXT / off critical path" item**~~ — **PROMOTED to the critical path.** It is the first artifact a stranger receives, it precedes the app itself, it lands in spam, and thirteen people have already received it. The reasoning that deferred it ("nobody's walking through that door yet") was built on a headcount that was wrong by nine.
- ~~**Automated avatar cropping / slicing**~~ — **DECLINED.** Four escalating attempts, four failures. Success criterion is visual; no agent in the loop can see. Manual GIMP.
- ~~**Seasonal frost cosmetic set**~~ — superseded by "cosmetic drop #2 — the roster surplus."
- ~~**Vocabulary enforcement pass**~~ — **DONE.** Audited with grep evidence and per-file justification. *Though note: the `Feast` label survived the audit and shipped anyway, because the audit checked strings and nobody checked whether the decision had landed at all.*
- ~~**Lists collapsible sections**~~ · ~~**Password confirm on signup**~~ · ~~**Campaign-detail filter bug**~~ — resolved or found not to exist.
- ~~**Pip onboarding flow (as a tour)**~~ — superseded. Three onboarding SCREENS, comprehension-only. See `decisions.md`.

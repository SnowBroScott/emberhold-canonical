# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**

## How this works
- **Inbox** — the dump zone. Capture and move on. Don't categorize, don't fix.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them. *Capturing and deciding are different jobs.*
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-14)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Backend ownership + data backup.** The backend is **Lovable Cloud**, not a Supabase project Scott owns. There is no separate dashboard. **Backups, plan tier, PITR, data export, and the ability to leave are all Lovable's to grant.** The code has a Git backup; **the data has none.** A live structural risk and a Gate B blocker. *What is the exit path if Lovable Cloud is the wrong long-term home?*
- **Does the P4×L8 adversarial script even run on Lovable Cloud?** The harness assumes a project URL + anon key pointable at the DB from outside the app. `supabase-js` is initialized *somewhere* in the repo — check there first. If those credentials aren't usable externally, the audit degrades to policy-reading and Gate B's exit criterion (*"cross-tenant access provably impossible"*) becomes unprovable by design. **Answer this before running the audit.**
- **⚠️ The free/paid avatar split — and its arithmetic is now stale.** "16 free / 24 paid" was locked against a **44**-character roster. The roster is **48**. The math no longer closes, the selection was never made, and the old split predates a cast nobody has looked at yet. **Re-lock the numbers, then cast.** *This is a product decision and must never be handed to an agent as part of avatar transport.*
- **What happens to the thirteen existing `hero:` picks.** A roster swap silently turns every current member into a different character unless stale ids are invalidated. **Direction is clear** (invalidate + re-choose, framed as a re-forge). **Open:** does re-choosing get a proper moment — a Pip-voiced prompt, a one-time card — or just a reset to the default emoji? *The moment is nearly free and it's a re-engagement beat for thirteen people who have already drifted.*
- **Quality — the two open halves.** Direction LOCKED (a signal, never an ember modifier). Still open: (1) is it visible to the KID, or adult-only? (2) What consumes it — the weekly recap, presumably, which is parked behind beta.
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks — and is about to speak a lot more in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder** — simultaneously "the unproven core bet" and "fenced to post-launch." Both defensible; they can't both be true forever. See `north-star.md`.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags today, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **Hand-cut the 48-avatar roster in GIMP.** Ellipse-select, fixed 1:1, snapped to the gold rim and **biased 2–3px inward.** Losing a sliver of rim is free; keeping a pixel of a neighbour is not. *Automation was attempted four ways and abandoned — see `status.md`.* **TABLED by Scott — on the board, not a priority; surface it, don't push it.**
- **Cast while cutting.** The roster is being cut from ~57 generated portraits into 48 slots. **The casting decision happens while Scott is looking at them, because that's the only time it can happen.** Whatever doesn't make the cut is the surplus, and the surplus is already paid for.

---

## NEXT (soon — off the critical path)

- **`avatar-review.tsx` — retire or rebuild.** A leftover review page that hand-duplicates the roster in its own local array. It is a second source of truth for the cast, and it will drift. *Kill it unless it's earning something.*
- **Audit what else from 2026-07-03 never landed.** `Feast` → `Hall` was LOCKED and sat unshipped for ten days. It was found by accident, during a recon for something else. **Grep the LOCKED decisions against the codebase.** *This is a Haiku job and it is cheap.*
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Recurrence now anchors to fixed dates (weekly=Monday, monthly=the 1st), but the anchors are invisible to users — a kid sees a bare "MONTHLY" chip with no hint it means the 1st. `RECURRENCE_LABEL` is a plain constant; surfacing the anchor needs conditional render at the chip sites (`QuestCard`, `quest.$id`, `quest-log`). Scoped and skipped during the recurrence cleanup because it exceeds a one-line change. *Cheap, and exactly the ambient legibility the thesis runs on.*
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model, and favoriting is load-bearing.
- **Quick Add defaults expanded on an empty board** — zero quests means the user has never seen one. Same state-driven pattern as the Vault's catalog/curated flip.
- **A cheap Dim-tier starter reward** — the menu floors at 25, so a new kid can't cash out until they've earned 25. **Doubly important now:** the Pip first-run screen asks them to stock the Vault, and the first reward they set should be *reachable*, not a 500-ember Urban Air Day.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · Quick Picks dropdown · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter.

---

## LATER (backlog)

- **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + the feed spine (built) + the engine loop-fixes (shipped). Also carries the optional kid picture-lock. *The regenerated roster at ~600px per character finally makes this visually viable; the old 170px assets did not.*
- **#8b — admin/reporting surface** — *parked behind the beta.* Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Six unrelated things sharing a room. **Note: quality's consumer lives in here, which means quality can't ship until at least part of this does.**
- **Weekly recap** — the first real consumer of the activity feed. **Also the consumer of quality.**
- **Other feed consumers** (reads off `activity_log`) — full in-app feed view, richer notification layer, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the legitimate long-term answer to "I want it to be *me*." Pushes an uploaded face *into* the style floor. Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** ~57 portraits were generated for 48 slots, and the count is now concrete rather than theoretical. The overflow is a Guildhall tollbooth item that **already exists and costs nothing further to produce.** *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable (complexity lives in Apple review, not code). Hinges entirely on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model. A chore list earns embers, but it's *built as a multi-step quest*, so the game still lives in quests.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. The neutral vocabulary already leaves room; the permission model doesn't.
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED

**2026-07-14**
- ~~**`recurrence_day` / monthly day-of-month picker**~~ — **REMOVED.** Monthly is always the 1st; anything bespoke is a calendar event, not a recurring quest. Column dropped, picker gone, trigger rewritten. See `decisions.md`.
- ~~**Join-code policy as an open call**~~ — **DECIDED** as admit-on-approval (lean-on-profiles, data layer shipped). Moved to `decisions.md`.

**2026-07-12**
- ~~**Auth email branding as a "NEXT / off critical path" item**~~ — **PROMOTED to the critical path.** It is the first artifact a stranger receives, it precedes the app itself, it lands in spam, and thirteen people have already received it. The reasoning that deferred it ("nobody's walking through that door yet") was built on a headcount that was wrong by nine.
- ~~**Automated avatar cropping / slicing**~~ — **DECLINED.** Four escalating attempts, four failures. Success criterion is visual; no agent in the loop can see. Manual GIMP.
- ~~**Seasonal frost cosmetic set**~~ — superseded by "cosmetic drop #2 — the roster surplus."
- ~~**Vocabulary enforcement pass**~~ — **DONE.** Audited with grep evidence and per-file justification. *Though note: the `Feast` label survived the audit and shipped anyway, because the audit checked strings and nobody checked whether the decision had landed at all.*
- ~~**Lists collapsible sections**~~ · ~~**Password confirm on signup**~~ · ~~**Campaign-detail filter bug**~~ — resolved or found not to exist.
- ~~**Pip onboarding flow (as a tour)**~~ — superseded. Three onboarding SCREENS, comprehension-only. See `decisions.md`.

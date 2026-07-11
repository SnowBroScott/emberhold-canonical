# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**

## How this works
- **Inbox** — the dump zone. Capture and move on. Don't categorize, don't fix.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them. *Capturing and deciding are different jobs.*
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty)*

---

## OPEN DECISIONS (unresolved — these are waiting on Scott)

- **Mascot name** — Cinder (kid vote) vs Holt (lore). Now pressing, since the mascot speaks.
- **Founding Guildhall tollbooth** — pricing, border art, and the finer free/paid roster split. (16 free / 24 paid is locked; *which* 16 is not.)
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder** — it is simultaneously "the unproven core bet" and "fenced to post-launch." Both defensible; they can't both be true forever. See `north-star.md` for the trigger condition.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags on two objects today, deliberately not unified. A sober-daylight refactor call *only if it earns its keep* — not built speculatively.

---

## NEXT (soon — but off the critical path)

- **Vault favorites → real per-profile persistence** — localStorage won't survive the shared-wall model, and favoriting is now load-bearing. *Build-to-validate; fix-before-ship.*
- **Vocabulary enforcement pass** — the string law is written but never audited. Lovable *reported* quest/bounty was already consistent; that's a claim, not a verification.
- **A cheap Dim-tier starter reward** — the menu floors at 25, so a new kid can't cash out until they've earned 25. A fast first redemption teaches the whole loop.
- **Remaining polish burn-down** — Lists collapsible sections · password confirm on signup · Pip install tutorial + help discoverability · Quick Picks dropdown · reward scarcity limits · campaign-detail filter bug · yearly/monthly event recurrence.

---

## LATER (backlog)

- **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + the feed spine (built) + the engine loop-fixes (shipped — it now has a working stage). The kiosk phase also carries the optional kid picture-lock.
- **#8b — admin/reporting surface** — *parked behind the beta.* Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Six unrelated things sharing a room; post-beta it may turn out to be two features instead of six, and nobody has used the app long enough to have anything worth analyzing.
- **Weekly recap** — the first real consumer of the activity feed. Computed-on-open per-member week summary.
- **Other feed consumers** (reads off `activity_log`) — full in-app feed view, richer notification/awareness layer, the Adventure Log, a family message board.
- **Seasonal frost cosmetic set** — leftover cool-register gens + a unified frost aesthetic. Household-level unlockable; second Guildhall tollbooth item. **Build after the base roster ships and validates** — don't build the winter DLC before the base game.
- **Photo → stylized-hero pipeline** — the legitimate long-term answer to "I want it to be *me*." Pushes an uploaded face *into* the style floor. Never a bare photo in the circle. Reuses the alpha-check asset pipeline.
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated, optional ember nudge. Deep future.
- **Capacitor / app-store path** — assessed viable (complexity lives in Apple review, not code). Deferred; hinges entirely on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model. A chore list earns embers, but it's *built as a multi-step quest*, so the game still lives in quests.
- **Flat / peer holds** — deeper power-structure flexibility (roommates, couples with no owner-above-peer). The neutral vocabulary already leaves room for it; the permission model doesn't yet.
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

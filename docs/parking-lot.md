# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**

## How this works
- **Inbox** — the dump zone. Capture and move on. Don't categorize, don't fix.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them. *Capturing and deciding are different jobs.*
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-12)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Backend ownership + data backup.** The backend is **Lovable Cloud**, not a Supabase project Scott owns. There is no separate dashboard. **Backups, plan tier, PITR, data export, and the ability to leave are all Lovable's to grant.** The code has a Git backup; **the data has none.** This is not a preference — it is a live, unresolved structural risk, and it is a Gate B blocker. *What is the exit path if Lovable Cloud is the wrong long-term home?*
- **Does the P4×L8 adversarial script even run on Lovable Cloud?** The harness assumes a project URL + anon key that can be pointed at the DB from outside the app. `supabase-js` is initialized *somewhere* in the repo — check there first. If those credentials aren't usable externally, the audit degrades to policy-reading and Gate B's exit criterion (*"cross-tenant access provably impossible"*) becomes unprovable by design. **Answer this before running the audit.**
- **Join codes — the design call inside the CRITICAL finding.** Possession of the string currently grants full adult admin. Expire them? Rotate them? Admit-on-approval? Rate-limit? **A linter cannot make this call and neither can an agent.**
- **Quality — the two open halves.** Direction is LOCKED (a signal, never an ember modifier). Still open: (1) is it visible to the KID, or adult-only? (2) What consumes it — the weekly recap, presumably, which is parked behind beta.
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks — and is about to speak a lot more in the first-run screens.
- **Founding Guildhall tollbooth** — pricing, border art, and *which* 16 avatars are free. (16 free / 24 paid is locked; the selection is not.) **This is a product decision and must never be handed to an agent as part of avatar transport.**
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it.
- **Display mode's position on the ladder** — simultaneously "the unproven core bet" and "fenced to post-launch." Both defensible; they can't both be true forever. See `north-star.md`.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags today, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NEXT (soon — off the critical path)

- **Auth email branding.** Sender is `no-reply@auth.lovable.cloud`; display name is `Family-Quest-Board`. It lands in spam. **It is the first artifact a stranger receives from Emberhold — it precedes the app itself.** Templates are editable in Lovable Cloud → Emails. *Note: when this is fixed, delete the holding page's line about the sender being "currently unbranded" — it's an honest tripwire, and it should stop being true.*
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model, and favoriting is load-bearing.
- **Quick Add defaults expanded on an empty board** — zero quests means the user has never seen one. Same state-driven pattern as the Vault's catalog/curated flip.
- **A cheap Dim-tier starter reward** — the menu floors at 25, so a new kid can't cash out until they've earned 25. **Doubly important now:** the Pip first-run screen asks them to stock the Vault, and the first reward they set should be *reachable*, not a 500-ember Urban Air Day.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · Quick Picks dropdown · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter.

---

## LATER (backlog)

- **Display / wall / kiosk mode** — the ambient-presence thesis. Rides on the realtime layer (unscoped) + the feed spine (built) + the engine loop-fixes (shipped). The kiosk phase also carries the optional kid picture-lock. *The regenerated roster at 600px+ per character finally makes this visually viable; the old 170px assets did not.*
- **#8b — admin/reporting surface** — *parked behind the beta.* Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Six unrelated things sharing a room. **Note: quality's consumer lives in here, which means quality can't ship until at least part of this does.**
- **Weekly recap** — the first real consumer of the activity feed. **Also the consumer of quality.**
- **Other feed consumers** (reads off `activity_log`) — full in-app feed view, richer notification layer, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the legitimate long-term answer to "I want it to be *me*." Pushes an uploaded face *into* the style floor. Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** 48 characters generated, more than the roster needs. The overflow is a Guildhall tollbooth item that already exists and costs nothing further to produce. *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable (complexity lives in Apple review, not code). Hinges entirely on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model. A chore list earns embers, but it's *built as a multi-step quest*, so the game still lives in quests.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. The neutral vocabulary already leaves room; the permission model doesn't. **Adjacent to finding #7** — the owner-vs-adult confusion in the policy layer is the same conceptual gap.
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED (2026-07-12)

- ~~**Seasonal frost cosmetic set**~~ — superseded by the broader "cosmetic drop #2 — the roster surplus" above.
- ~~**Vocabulary enforcement pass**~~ — **DONE.** Audited with grep evidence and per-file justification (7/11 and 7/12). The method itself was upgraded: grep case-insensitively, against the SOURCE value, because CSS `text-transform` means the string in the code is not the string on the screen.
- ~~**Lists collapsible sections**~~ · ~~**Password confirm on signup**~~ · ~~**Campaign-detail filter bug**~~ — all resolved or found not to exist. See `status.md`.
- ~~**Pip onboarding flow (as a tour)**~~ — superseded. It is now three onboarding SCREENS, comprehension-only, no staged magic moment. See `decisions.md`.

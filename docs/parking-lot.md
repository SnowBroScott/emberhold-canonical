# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-18 (evening).*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-18 evening)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the same Supabase backend as production. Fine at Alpha; a real Gate-B concern once strangers are on it.
- **⚠️ Backend ownership + data backup.** Backend is Lovable Cloud; no direct Supabase access. Backups/PITR/export/exit are Lovable's to grant. Data has no backup. A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **Does the P4×L8 adversarial script even run on Lovable Cloud?** If a project URL + anon key aren't pointable at the DB from outside, the audit degrades to policy-reading. **Answer before running the audit.** Named inputs now include the wall's two SECURITY DEFINER writes AND the founder-gate DB objects (see below).
- **QA #5 — in-hold admin tier vs cross-hold super-admin.** Reclassified 07-18 as distribution-era support tooling (see decisions.md), but a residual ambiguity is open: does Scott want (a) a permission tier *inside* a hold, above parent, that manages that hold's quests/embers/members/feed — or (b) a cross-hold super-admin that reaches into any family's hold for tier-2 support? Different builds; (a) changes the role model, (b) is cross-tenant / P4×L8. **Waiting on concrete examples from Scott before scoping.**
- **The founder paywall flip — timing only, mechanism is built.** Not a build decision; a *when* decision. When Scott's ready: (1) grandfather — set every existing household `families.is_founder = true`; (2) flip — `system_flags.founder_gate_enabled = true`. Both are one-line data changes via Lovable, no build. Selling to *new* strangers still needs Stripe (critical-path #5), decoupled on purpose. **Not flipped 07-18 — Scott chose to sleep on it; nothing time-sensitive.**
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it (the weekly recap, parked behind beta).
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it. *(Ranks is a wall accordion zone AND now has per-member completed-quest expand — a richer surface for the same data.)*
- **Display mode's position on the ladder — PARTIALLY RESOLVED 2026-07-17.** The unproven half is answered (built for own-hold, it works). What's genuinely fenced is the distribution-grade version (deployment hardening + P4×L8 on its write surface). Still first off the fence if Gate E comes back flat on day-8.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **The floor — avatar render fallback (BUILDING at 07-18 close, 🟡 glass-verify pending).** Any unresolved/invalid pick renders a clean ember-lit placeholder, never the raw key. Verify on the glass (a not-yet-re-forged member shows a placeholder, not a number; check the wall). See status.md PENDING VERIFY.
- **Founder tier-tag verification (needs a gate-on moment).** Briefly flip the gate ON → confirm exactly 32 lock / 16 open → flip back OFF. The only check that can't be done gate-off. See status.md.

*(Avatar cutting/casting/batch-export — DONE. The 48 were cut, cast by slot-name, and transported 07-18. Removed from NOW.)*

---

## NEXT (soon — off the critical path)

- **Haiku sweep (queued, read-only, after transport settled):** grep repo for `Feast` → swap any user-facing hit to `Hall` (**confirmed live on the member editor 07-18**, not just the dev page as docs claimed); grep every LOCKED decision against the codebase (the "did it land" cross-check); retire/rebuild `avatar-review.tsx` (second source of roster truth, and the only home of the Forge/Garden/Keep/Hall HeroGroup vocab — kill unless earning something; note it still references the old `avatar-01…21` files).
- **Re-forge reach across the 13 (real finding — see status.md).** When the re-forge rolls to real testers, only the logging-in member is prompted; the rest render the fallback until manually fixed. Options: prompt each member on next active session, or a hold-owner "N hearthmates need re-forging" nudge. A calm, later decision.
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Anchors invisible. Cheap conditional render.
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model. Build phone-first; the wall inherits it (as a `wall_toggle_favorite` on-behalf write — another SECURITY DEFINER / P4×L8 line).
- **Wall Vault empty-state — two-case branch once persistent favorites ship** (affordability vs no-favorites; correct verb per cause).
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward** — the menu floors at 25; shrinks the wall's empty-Vault case; seeds the Pip "stock the Vault" screen.
- **Wall event-pill member color — dot-vs-full-tint** legibility-at-distance refinement.
- **Wall ticker speed** — tune-by-eye legibility knob, not a rebuild.
- **Multi-day calendar events (QA #7).** Events spanning day boundaries (Fri 5pm → Sat 3pm). Data-layer + rendering build; the event model likely assumes single-day. Real, its own prompt.
- **Calendar alerts (QA #6).** The useful version ("ping me before") needs scheduled push, which is fenced. A lighter in-app-reminder version might ride the existing notification layer. Decide push-vs-in-app before building.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · feed verb drift ("New quest: …" vs "QUEST POSTED").

---

## LATER (backlog)

- **The "how Scott & jAIne work" collaboration profile.** Scott's own Claude instructions / personal profile, so every cold session starts from the best footing. Encodes the working relationship (register, lane-discipline, recon-first, the "surface the instinct then test it" loop, no-ego-stroking, tersity). Its own deliberate sober-daylight session — NOT a drive-by, NOT a project doc. *(Adjacent-relevant 07-18: the session-close protocol miss reinforced why durable, in-context rules beat "jAIne remembers." The close-out law is being baked into project instructions directly for the same reason.)*

- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The one identity-first word in a function-first vocabulary. The real distinction is approver vs. submitter. A display-string sweep, not a data migration. Its own deliberate session, ideally bundled with another vocab pass (e.g. the Feast→Hall sweep).

- **Display / wall / kiosk mode — v1 FOR OWN-HOLD.** See status.md for the built ledger, decisions.md for the trust model. Still fenced for the STRANGERS-grade version:
  - **Wall redemption/spend commit — future build, not yet scoped.** No pending-redemptions surface on the wall to attach to; that must be built first. Same adult-verify model. Keep OFF the wall until deliberately built.
  - **Deployment gremlins** — old iPad mini: Safari version, PWA display-mode behavior, screen-sleep, 4:3 ~1024×768.
  - **P4×L8 on the wall's write surface** — `wall_request_redemption` + the commit-attribution class.
  - **Favorites on the wall** — deferred to per-profile persistence.

- **#8b — admin/reporting surface** — parked behind beta. Redemption history, reward performance, weekly recap, Adventure Log, ops glance. Quality's consumer lives here. *(Distinct from QA #5's support-role clarification — #8b is in-hold reporting; #5 is cross-hold support authority.)*
- **Weekly recap** — first real consumer of the activity feed + quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term "I want it to be *me*." Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** ~57 portraits generated for 48 slots; the overflow is a Guildhall tollbooth item that already exists. *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable; hinges on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. Vocabulary leaves room; the permission model doesn't.
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED

**2026-07-18 (evening)**
- ~~**"What happens to the thirteen existing `hero:` picks" (open decision)**~~ — **RESOLVED.** Re-forge shipped: invalidate + route to `/reforge-avatar`, single-prompt pick with an on-theme "re-forged" line. 28/28 profiles invalidated. Spawned a new finding — **re-forge reach across the 13** (see NEXT / status.md).
- ~~**"The free/paid avatar split — arithmetic is stale" (open decision)**~~ — **RESOLVED at 16/32.** Scott set it by hand, sorting the 48 cut files into two folders (16 free / 32 founder, 4+8 per class). The folders were the casting. See decisions.md.
- ~~**Re-forge Pip-moment sub-question** (does re-choosing get a voiced moment or a silent reset)~~ — **RESOLVED:** single-prompt with a short on-theme line, shipped.
- ~~**"Founder gate as bespoke gating system"**~~ — **SUPERSEDED by the entitlement-seam design** (DB-value gate + `families.is_founder`, reusing the Guildhall flag). See decisions.md.
- ~~**"A session wrap / summary doc"**~~ — **NEVER A REAL ARTIFACT; DISCARDED.** jAIne invented a combined "wrap" file and buried decision blocks + instructions inside it — a session-close protocol violation on three counts. Corrected: close = four named deliverables in four fixed formats (status/parking-lot as files, decisions as a bare in-chat block, master-spec call stated), no wrapper. The close-out law is being baked into project instructions.

**2026-07-18 (day)**
- ~~Identity-bound wall commit~~ — walked back to adult-verify.
- ~~SnowDad Vault spendable = 0 as a bug~~ — resolved as test cruft.
- ~~"Scott sets every status" doc rule + compliance checkpoint~~ — discarded, never canon.
- ~~Automated / hand-cut avatar circles~~ — superseded by fill-corners; now fully moot (transport shipped).

**2026-07-17**
- ~~Wall calendar event detail / class-color pills / realtime data / Rewards accordion~~ — shipped-or-corrected (see git history).
- Wall interaction layer — BUILT.

**2026-07-16 / 07-15 / 07-14 / 07-12** — *(preserved from prior revisions: roster grant hole, pending-member routing, "all quiet" resolved, privilege-escalation fixes, recurrence rework, join-code → admit-on-approval, auth email promoted, automated avatar cropping DECLINED, vocabulary pass. See git history.)*

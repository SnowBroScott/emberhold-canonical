# Parking Lot

**What might be.** Captured, not committed. One place for every stray idea, so nothing derails the build.

> Decisions live in `decisions.md`. Status lives in `status.md`. **This doc holds only what hasn't been decided yet.**
> *Complete dated replacement each session. Last: 2026-07-18.*

## How this works
- **Inbox** — the dump zone. Capture and move on.
- **Triage** — once per session, move Inbox lines into Now / Next / Later, or kill them.
- **Live shortcut** — say `park: ...` and jAIne logs it without derailing the thread.

---

## Inbox (untriaged)

*(empty — triaged 2026-07-18)*

---

## OPEN DECISIONS (unresolved — waiting on Scott)

- **⚠️ Staging / dev database — do we need one before beta?** Local dev points at the **same Supabase backend as production**. Every local-dev test writes to the same tables as real users' data. Fine at Alpha; a real Gate-B concern once strangers are on it. Related to the P4×L8 harness-reachability question below.
- **⚠️ Backend ownership + data backup.** Backend is **Lovable Cloud**, not a Supabase project Scott owns. **No direct Supabase access.** Backups, PITR, export, and the ability to leave are all Lovable's to grant. Code has a Git backup; **the data has none.** A Gate B blocker. *What's the exit path if Lovable Cloud is the wrong long-term home?*
- **Does the P4×L8 adversarial script even run on Lovable Cloud?** The harness assumes a project URL + anon key pointable at the DB from outside. If those aren't usable externally, the audit degrades to policy-reading and Gate B's exit criterion becomes unprovable by design. **Answer before running the audit.** *(2026-07-17: two SECURITY DEFINER writes are now named inputs — the 07-16 Data-API grant surface and `wall_request_redemption`'s household-scope check.)*
- **⚠️ The free/paid avatar split — arithmetic is stale.** "16 free / 24 paid" was locked against a 44-character roster; the roster is 48. Re-lock the numbers, then cast. *A product decision — never handed to an agent as part of avatar transport.*
- **What happens to the thirteen existing `hero:` picks.** A roster swap silently remaps everyone unless stale ids are invalidated. Direction clear (invalidate + re-choose, framed as a re-forge). Open: does re-choosing get a Pip-voiced moment or just a reset?
- **Quality — the two open halves.** Direction LOCKED (signal, never an ember modifier). Open: (1) visible to the kid or adult-only? (2) what consumes it (the weekly recap, parked behind beta).
- **Mascot name** — Cinder (kid vote) vs Holt (lore). Pressing, since the mascot speaks in the first-run screens.
- **Ranks as a household dial** — soften the sibling-ladder into private progress. jAIne has a lean; Scott hasn't called it. *(Ranks is also a wall accordion zone now — a second surface for the same data.)*
- **Display mode's position on the ladder — PARTIALLY RESOLVED 2026-07-17.** Was "simultaneously the unproven core bet and fenced to post-launch." **The unproven half is now largely answered: it's BUILT for Scott's own hold and it works** — the ambient-presence thesis has a working, interactive artifact, not just a description. What remains genuinely fenced is the DISTRIBUTION-grade version (deployment hardening + P4×L8 on its write surface). The tension is smaller now: not "will it work," but "when does the strangers-grade version graduate off the fence." Per north-star, still first off the fence if Gate E comes back flat on day-8 retention.
- **Unify `quest.audience` and `reward.audience`?** — two parallel flags, deliberately not unified. A sober-daylight refactor call *only if it earns its keep*.

---

## NOW (this is the next work)

- **Hand-process the 48-avatar roster — CUT-METHOD SOLVED 2026-07-18.** Not a cut — a **fill**: the app clips every avatar into a circular frame, so the only failure mode is white corners peeking past the mask. Ellipse-select inscribed in the square (corner-to-corner) → invert → fill corners `#1A110B` (the spec's dark, no alpha) → flatten → scale 512×512 → export as the slot name. Do NOT select-by-color (kills grey hair, eye-glints); target corners by shape. Dark-on-dark seam means pixel-perfect alignment isn't required. Proven on the two hardest cases (white-haired elder, fire-everywhere dragon). **Still TABLED by Scott — surface, don't push.**
- **Cast while cutting.** Cut from ~57 generated portraits into 48 slots; the casting decision happens while Scott is looking at them — the one part that can't batch. The surplus is already paid for. **Name each `.xcf` as its slot** (`forge-01.xcf` … `hall-12.xcf`) so the batch script just swaps `.xcf`→`.png`; the slot IS the cast, made durable.
- **Batch export script — READY TO WRITE on Scott's word.** Once casting's done, a headless GIMP Script-Fu (or ImageMagick one-liner) does fill → flatten → scale 512 → export across all 48 in one run — no one-by-one. jAIne writes it when Scott says the roster's cut. Reusable for later recuts/additions.

---

## NEXT (soon — off the critical path)

- **`avatar-review.tsx` — retire or rebuild.** A leftover review page that hand-duplicates the roster; a second source of truth that will drift. *(2026-07-17 note: this file is also the ONLY home of the "Forge/Garden/Keep/Hall" HeroGroup vocabulary, which is avatar-art categorization — NOT a member trait and NOT a color system. See the class-color correction in decisions.)* Kill it unless it's earning something.
- **Audit what else from prior sessions never landed.** `Feast` → `Hall` was LOCKED and sat unshipped ten days. **Grep every LOCKED decision against the codebase.** *Haiku job, cheap, overdue.* **(2026-07-17 reinforced the inverse too: the DOCS can lie about the CODE — the wall shipped while the docs called it fenced. Cross-check runs both directions now: unshipped decisions AND undocumented shipped work.)*
- **Recurrence chip legibility — "Weekly · Mon" / "Monthly · 1st".** Anchors invisible to users. Needs conditional render at the chip sites. Cheap; exactly the ambient legibility the thesis runs on.
- **Vault favorites → real per-profile persistence** — `localStorage` won't survive the shared-wall model. **The wall already exposed this:** its Vault is affordable-only precisely because per-member favorites can't be read from one shared login's localStorage. Build as the phone-Vault's per-profile persistence job → the wall inherits it free. **On the wall this is an on-behalf write** (like `wall_request_redemption`), so a `wall_toggle_favorite` proxy is likely needed — another SECURITY DEFINER surface, another P4×L8 line. Build it in the phone-first direction, not backwards through the wall.
- **Wall Vault empty-state message — becomes a two-case branch once persistent favorites ship.** The curated popup Vault can be empty for two independent reasons: (1) **affordability** — balance below the cheapest reward → nudge toward earning (current message, keep it); (2) **no favorites selected yet** → nudge toward favoriting in the full Vault. Same warm on-theme pattern, but the copy must point at the correct verb per cause (don't tell a rich-but-unfavorited kid to go do chores). If both true, likely lead with earn. Not buildable until favorites persistence exists; captured so "keep the empty message" doesn't ship the wrong verb.
- **Quick Add defaults expanded on an empty board.**
- **A cheap Dim-tier starter reward** — the menu floors at 25, so a new kid can't cash out until they've earned 25. **Doubly important now:** the wall's affordable-only Vault shows an empty state below the floor; a reachable starter reward shrinks that empty case. Also what the Pip first-run "stock the Vault" screen should seed.
- **Wall event-pill member color — dot-vs-full-tint.** Event pills now carry `memberColor()` (SHIPPED 2026-07-17). Open refinement: full-tint vs. a class-colored dot for legibility-at-distance. A "see it both ways" when wall polish resumes. *(Note the mechanism: memberColor identity color, NOT a class system — the "class" framing was a myth, corrected in decisions.)*
- **Wall ticker speed** — a tune-by-eye legibility knob now that the loop is seamless. Not a rebuild.
- **Remaining polish burn-down** — `TITLE`→`QUEST TITLE` · Pip install tutorial + help discoverability · Quick Picks dropdown · reward scarcity limits · yearly/monthly event recurrence · Lists "348 DONE" fossil counter · **feed verb drift** ("New quest: …" vs the standardized "QUEST POSTED").

---

## LATER (backlog)

- **The "how Scott & jAIne work" collaboration profile.** *(new 2026-07-17)* Not a project doc and not project instructions — **Scott's own Claude instructions / personal profile**, so every cold session in every project starts from the best possible footing. Encodes the WORKING RELATIONSHIP (register, lane-discipline, the "surface the instinct then test it" loop, the "don't stroke my ego" rule, tersity, recon-first) into something durable that survives the fact that jAIne is reconstructed fresh each session. The project's canonical repo does this for the PROJECT; this does it for the COLLABORATION. **Its own deliberate session — a sober-daylight artifact, NOT a drive-by, and explicitly not started mid-build.** Carries over the exercise Scott already ran with Copilot (professional side).

- **Retire identity-first role labels — "Parent/Kid," especially "Kid."** The one identity-first, literal, flat-English word in an otherwise function-first themed vocabulary. Ages badly. The real distinction is **approver vs. submitter** — an authority model already implemented correctly; "Adult/Kid" is only the skin. A display-string sweep, not a data migration (the `parent/kid` enum stays under the hood). Wide surface, 13 people with the current model in muscle memory. Its own deliberate session, ideally bundled with another vocab pass. **Adjacent to "Flat / peer holds" below — labels half vs. permission-model half.**

- **Display / wall / kiosk mode — v1 FOR OWN-HOLD (propose tier 2026-07-17 · adult-verified turn-in commit 2026-07-18).** *(Was "design prototyped, not wired.")* See `status.md` for the full built ledger and `decisions.md` for the trust model. Propose tier + on-ramp/idle/exit/ticker/poll/event-detail shipped 07-17; adult-verified turn-in commit (mint) building 07-18 (🟡 glass-verify pending). **The three laws held and drove the 07-17 build** (spatial → Vault-in-tile + picker split; legibility-at-distance → tile scaling; three-zone accordion preserved). **PROMOTION NOTE:** when display graduates from fenced-delight to distribution roadmap (post-beta, or Gate-E-flat), promote the three laws to `decisions.md` / `master-spec.md`. Still fenced for the STRANGERS-grade version; what remains:
  - **Wall redemption/spend commit — future build, NOT yet scoped.** v1 commit covers quest turn-in (mint) only. Redemption-commit (approve a spend from the wall) has **no surface to attach to** — recon found no pending-redemptions view on the wall (turn-ins have one; redemptions don't). That surface must be built first. Same adult-verify model applies; keep it OFF the wall until deliberately built.
  - **Deployment gremlins** — old iPad mini: Safari version, PWA display-mode behavior, screen-sleep (Guided Access / never-sleep + dock), 4:3 ~1024×768. Real, deferred.
  - **P4×L8 on the wall's write surface** — `wall_request_redemption` household-scope, plus (07-18) the commit-attribution class (`approved_by` client-asserted, `decided_by` session-locked). See decisions + status critical path.
  - **Favorites on the wall** — deferred to per-profile persistence (see NEXT).
  - **The idle-return-to-ambient timer** is BUILT; the resting state is the named config (Bounties collapsed / Hearth expanded / Ranks collapsed / left = launch choice).

- **#8b — admin/reporting surface** — *parked behind the beta.* Redemption history, reward performance, weekly recap, Adventure Log, ops glance. **Quality's consumer lives here** — quality can't ship until part of this does.
- **Weekly recap** — the first real consumer of the activity feed. Also the consumer of quality.
- **Other feed consumers** — full in-app feed view, richer notifications, the Adventure Log, a family message board.
- **Photo → stylized-hero pipeline** — the long-term answer to "I want it to be *me*." Never a bare photo in the circle.
- **Cosmetic drop #2 — the roster surplus.** ~57 portraits for 48 slots; the overflow is a Guildhall tollbooth item that already exists. *Don't build the winter DLC before the base game.*
- **"Spin for a quest"** — random weighted quest assignment, kid-initiated. Deep future.
- **Capacitor / app-store path** — assessed viable; hinges on whether payment rails are ever needed. Currently: no.
- **List → quest hook** — deferred to the parked `objectives[]` / multi-step-quest model.
- **Flat / peer holds** — roommates, couples with no owner-above-peer. Vocabulary leaves room; the permission model doesn't. *Labels half = role-label retirement above; this is the permission-model half.*
- **iCal import** — never ask anyone to abandon their work calendar. Import theirs instead.

---

## KILLED / SUPERSEDED

**2026-07-18**
- ~~**Identity-bound wall commit (tap SnowDad → SnowDad's own PIN → decided_by = proven adult)**~~ — **WALKED BACK to adult-verify.** Recon showed identity-bound attribution is a data-layer P4×L8 build (`approve_redemption` hardcodes `decided_by=auth.uid()`; `approved_by` is client-asserted with no server tie). The actual threat is a kid self-committing, which adult-verify fully stops. See decisions.
- ~~**SnowDad Vault spendable = 0 as a bug**~~ — **RESOLVED as test cruft.** Debit-side: three June test self-redemptions (150) > earnings (106), clamped. Cleaned up. See status + decisions (two real findings logged: `parent_self_redeem` feed blind spot, silent overdraw clamp).
- ~~**"Scott sets every status" doc rule + close-out compliance checkpoint**~~ — **DISCARDED, never was canon.** 07-17 over-correction that misdiagnosed sloppiness. Model restored: jAIne sets statuses as judgment, Scott overrules. See status.
- ~~**Automated / hand-cut avatar circles**~~ — **SUPERSEDED by fill-corners method** (app clips the circle; fill `#1A110B`, don't cut). See NOW.

**2026-07-17**
- ~~**Wall calendar event detail (parked, bundle with color pills)**~~ — **SHIPPED.** Both existed on the phone (`EventDetail` + `memberColor()`); the wall now reuses them — tappable pills → in-place view-only detail sheet, member-colored pills. Edit/Delete suppressed on the wall.
- ~~**"Color wall event pills by class (Forge/Garden/Keep/Hall)"**~~ — **CORRECTED & SHIPPED as memberColor.** No class-color system exists; the mechanism is `memberColor()` identity color. See decisions.
- ~~**"Live" / realtime wall data**~~ — **CORRECTED to interval polling (~10s), decoupled from the ticker.** Realtime/websocket explicitly rejected. See decisions.
- ~~**"Rewards" accordion zone on the wall**~~ — **REMOVED same session.** Off-vocabulary and mis-scoped (person-scoped/spending surface in a household-glanceable column); the Vault moved into the member popup. See decisions.
- **Wall interaction layer (claim/turn-in/redeem/on-ramp/idle/exit/ticker/poll/event-detail)** — BUILT this session. Moved from "prototyped, not wired" to feature-complete for own-hold. See status + decisions.

**2026-07-16**
- ~~Pending members bounced onto setup form on re-login~~ — FIXED (`28ab40d`). *🟡 pending live repro.*
- ~~Roster / switch-picker "no members" bug~~ — FIXED (Lovable, live — Data-API grant hole).
- ~~"All quiet at the hold" as an empty-board bug~~ — RESOLVED working-as-intended.
- ~~Wall-display design session~~ — superseded: the design was built out 2026-07-17.

**2026-07-15 / 07-14 / 07-12** — *(preserved from prior revisions: recurrence_day removal, pending-member leak fix, admit/deny enum crash, walk-up escalation resolved, recurrence rework, join-code → admit-on-approval, auth email promoted to critical path, automated avatar cropping DECLINED, vocabulary pass, Pip onboarding-as-tour superseded. See git history.)*

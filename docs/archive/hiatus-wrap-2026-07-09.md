# Emberhold — Hiatus Wrap-Up
Consolidates the entire "Hiatus Parking Lot" running-thread (2026-06-30 → 2026-07-09) into dated, statused entries. This thread was deliberately held in-conversation rather than written to files mid-hiatus — this document is that promotion. No Git; treat this as the canonical record of the hiatus period until it's folded into the master spec / build-state / parking-lot proper at 7/10 session open.

Last updated: 2026-07-09

---

## Headline: one real fire, and it's out

The hiatus wasn't all captures-for-later. One item stopped being a parking-lot entry and became a live production bug fix, run entirely from mobile, under a credit crunch: **a fresh household could not add any member at all.** That's now resolved (pending one retest). Everything else below is genuinely banked, not built — the credit situation didn't allow more, and that constraint is what produced the length of this list in the first place (see Reflection, bottom).

---

## SHIPPED THIS HIATUS

### New-household member-creation blocker — RESOLVED (pending cold retest)
External tester Phaeaz hit `permission denied for table users` on every profile write on a fresh signup — couldn't add a kid, add an adult, rename, or change a pfp. Root cause and fix, in two parts:

**Part 1 — the crash (kid-add path).** The `profiles` INSERT policy's `WITH CHECK` ran `NOT EXISTS (SELECT 1 FROM auth.users u WHERE u.id = profiles.id)` as the `authenticated` role, which has no SELECT grant on `auth.users`. Postgres threw the grant-level error before RLS logic even evaluated — killing every profile write identically. Fix: dropped the redundant subquery from the policy. Protection is unchanged — `enforce_profile_insert_rules` (SECURITY DEFINER) already loops-until-no-collision against both `public.profiles` and `auth.users`, so the policy check was pure redundancy riding a table it wasn't allowed to touch.

**Part 2 — the adult-add rejection (separate cause, same report).** The INSERT policy permits `role = 'kid'` only — intentional anti-cheat, so no client path can mint or escalate into a parent row. But the add-family UI let users pick "Adult" and insert `role: 'parent'` directly, which RLS correctly rejected. Fix: adult creation now routes through a new `create_adult_profile` RPC (SECURITY DEFINER) — parent-gated caller check, server-generated collision-safe id, hashed PIN via the existing `set_profile_pin` mechanism. Kids keep the original raw-insert path; the kid-only INSERT policy stays locked exactly as designed.

**Status:** both fixes deployed. **Retest still needed** — ideally Phaeaz re-running his exact cold-signup flow (add kid → should work; add adult with valid PIN → should work; add adult with bad/blank PIN → should reject cleanly, not crash). Grandfathered households can't fully vouch for the new-user path — that's the whole reason this hid from internal testing for as long as it did.

**Debt surfaced, not addressed:** pre-existing `SECURITY DEFINER` linter findings (Lovable called them "unrelated" — they're deferred, not dismissed) now include one more definer function in the pile. Belongs on the pre-distribution security-hardening pass alongside join-code rotation (below).

---

## THE BURN-DOWN LIST — promoted from NOTED to tracked, statused honestly

None of these are built. All are reasoned, deduped, and ready to become build prompts. Grouped by where they land, not by discovery order.

### Engine / lifecycle (bundle — these three interlock)
1. **Adult quest completion auto-approves.** When the submitter is an Adult, `approved_by` is set to the completing adult atomically on completion — the mint fires in the same action. This is NOT a gate bypass: the approval requirement is auto-*satisfied* (an adult is already a trusted validator by role definition), not skipped. Kid completions unchanged — still queue for adult approval. Keys on submitter role, not creator or assignee.
2. **Daily recurring quest bug + midnight respawn.** Completed daily instances aren't leaving the active board (violates existing spec: archive-and-spawn, never reset in place). Fix is a date-window on the board (render only quests due today-or-earlier) rather than a scheduled job — ties to no-service-worker (#9 below). Spawn-next dates to tomorrow, appears invisibly until the day boundary arrives. New per-cadence rollover rule: **daily resets fresh, missed dailies don't accumulate** (a guilt-pile of undone dailies fights the habit loop); weekly/monthly missed instances may legitimately persist as still-due.
3. **Audience / edit-quest parity gaps.** Open Bounties not filtering by the `audience` flag is spec-compliant-deferred (documented as landing with the wall/display rail) — not a bug. The edit-quest form missing the audience selector **and** the monthly recurrence selector, despite both existing on create-quest, **is** a real parity bug — the edit modal never got the 6/29 additions.

> These three touch the same completion/lifecycle surface. Build as one pass, not three separate prompts.

### Copy / vocabulary
4. **Kill "XP" as user-facing vocabulary.** Embers is the sole currency term — XP implies a leveling system Emberhold doesn't have. Two views of embers stay: lifetime earned (Ranks) and spendable balance (Vault draws from this). Campaign progress bars are a bare percentage with no currency noun attached (earning-type campaigns showing a real pooled-ember count are the only exception — that's genuine currency, not a progress synonym). The `points` field name in the data model is unchanged — internal, user-invisible, no churn for zero benefit. Sweep both the master spec and live app copy; the app copy is the surface that drifts if only the doc gets fixed.
5. **Quest vs. bounty vocabulary rule.** Wording is currently interchanged across the UI. The rule the model already implies: **quest** = the object generally (and specifically a directed/assigned one); **bounty** = an open, unassigned quest anyone can claim. Apply consistently; don't pick one word and discard the other. Same copy-sweep bucket as #4.
6. **Pre-auth "Hold" grammar rule.** Themed vocabulary is fine pre-authentication when the grammar *grounds* it — imperative/doorway framing ("Enter the hold") teaches the word through the verb, no prior knowledge needed. It's the possessive cold use ("Your hold's quest board," "Your hold's whole world") that fails — possessive presumes the reader already knows the term. Fix: keep "Enter the hold" as-is; rework the two possessive taglines to plain language ("home"/"household"). The existing "WHAT IS EMBERHOLD?" explainer block already carries plain-language comprehension below the fold and stays as the safety net.

### Vault
7. **Vault balance-anchored curation.** Per cost tier, auto-surface a tight set anchored on the viewing profile's current balance: "in reach now" (~2 rewards at/just-under balance, glowing — the cash-out set) + "next unlock" (~1 just above balance — the automatic aspiration/taunt slot). Favorites/pins are **additive**, not an override — a pinned reward surfaces *alongside* the balance-driven sets, never bumping an auto-surfaced slot out. Everything else folds into its tier, one tap to expand. Rejected: pure highest-redeemed auto-surface (a popularity loop that structurally buries the aspirational big-ticket reward) and favorites-as-primary (manual curation rots; an empty-state favorites list on day one ships broken).
8. **Vault splits into two role-specific views**, mirroring the Home role-split pattern (same nav slot, different surface, same data). Kid view = #7 above. Adult view = an operations lens over the same reward pool — pending redemptions, redemption history, reward performance (what's never picked — prune candidates). No new schema; render/filter logic keyed on viewer role, same as Home.
9. **Reward scarcity limits.** Add limited-redemption controls to rewards — total stock, or per-user-per-period caps ("2 movie nights a month"). Vault v2 depth; adds real economic texture. *(Surfaced independently by Phaeaz.)*
10. **Quick Picks polish.** Expand quick-pick options; convert the quick-pick selector to a dropdown, matching the pattern already used in quest creation. Small, UI-parity work. *(Phaeaz.)*

### Lists
11. **Collapsible sections.** Sections default collapsed on open, showing only unchecked items. Each header gets an expand arrow + count chip ("▸ Dairy — 12 stocked") to reveal checked/struck items on demand. Collapse state always resets to collapsed on list open — not persisted per session. Pure render-state addition; no data model change.
12. **List suggestions in onboarding.** Extend the existing starter-quest-library pattern to Lists — offer pre-generated list suggestions as an onboarding step. Cheap reuse, low priority. *(Phaeaz.)*

### Onboarding / auth
13. **Password confirm-entry on signup.** Add a confirm-password field, mirroring the PIN's existing confirm-entry. A mistyped signup password is a harder lockout than a mistyped PIN (no adult-resets-adult fallback for the account owner — only Supabase's email reset). Pure parity with an already-shipped pattern.
14. **Pip install tutorial.** A Pip-guided "Add Emberhold to your home screen" card on the profile screen + a matching row in Pip's help sheet. Must be platform-detected (iOS Safari share-sheet / Android Chrome `beforeinstallprompt` / desktop address-bar icon each need different steps) and must suppress itself once already installed (`display-mode: standalone` check). Backend-free — rides on the manifest, which is confirmed working. Carved off the larger, backend-heavy Push+Install initiative; correct to build *before* push, not with it.
15. **Pip help discoverability.** An external tester didn't recognize the Pip icon as help — read it as a quick-access menu. Real signal on the one help affordance; needs more visual emphasis / signposting. Ties directly to #14. *(Phaeaz.)*

### Calendar
16. **Yearly + monthly event recurrence.** Birthdays and holidays want yearly; quests already have working monthly day-of-month machinery to borrow the pattern from. *(Phaeaz.)*

### Campaigns
17. **Campaign detail view bug.** An untagged/unassigned bounty is showing up inside a campaign's detail view even though the campaign correctly shows 0 of 0 in list view. The detail query is almost certainly missing its campaign-tag filter. Straight bug, not a design question. *(Phaeaz.)*
18. **Weekly recap — Adventure Log's first feature.** A button/badge appears once the week rolls, opening a per-member list of bounties completed + embers earned that week, expandable per-person to the full completed list. Trigger is computed-on-open (check if this week's recap exists on load, generate if not) — not a scheduled job, consistent with the daily-respawn date-window and the render-time feed-heat pattern, and honest about current infra (no service worker — see #26 below). Visibility: whole-household — everyone sees everyone's numbers. This is a pure query over data already written on every quest approval; zero new data model. Notably: a softer, retrospective, week-scoped cousin of a live leaderboard — may be exactly the gentler competitive framing #20 (Ranks) is looking for.

### Security / trust (pre-distribution hardening — bundle these before Founding Guildhall ships to strangers)
19. **Join-code policy** — an open design call. Specifics held privately until patched. *(Phaeaz — his sharpest catch.)*
20. **Kid-vs-kid impersonation — confirmed, not previously scored.** On the shared-device model there is one auth session; "acting as" a profile is an ungated client-side selection. Any kid can act as any other kid — claim, complete, redeem, edit avatar — with zero auth boundary behind it (the adult PIN is the system's only real gate). Blast radius is partly contained by existing approval gates: earn side fully protected (nothing mints without adult approval); spend side approval-gated (adult in the loop before embers leave a balance). Residual real harm: recoverable griefing, plus the sharper risk of an adult rubber-stamping an impersonated redemption because it looks legitimate. **Resolution:** kid PINs stay OFF by default — mandatory locks would break the frictionless walk-up thesis for device-less kids. An optional, lightweight, kid-appropriate lock (picture/item password — Phaeaz's own suggested mechanism) ships as a *feature of the wall/kiosk lock-screen phase* (where the threat actually lives), not a standalone build. Cheap mitigation available now regardless of phase: the redemption-approval card should clearly show WHO is redeeming (name + face) so an adult can catch impersonation at the point of approval — zero new machinery, reuses the human already in the loop.
21. **SECURITY DEFINER audit.** Standing linter findings, now one function larger with `create_adult_profile`. Needs a clean pass before Founding Guildhall touches strangers' data. Bundle with #19 and a general parent-gating audit of all definer RPCs.

### Product direction (your call, not mine — flagged with a lean, not a decision)
22. **Ranks as a household dial, not a binary toggle.** Phaeaz's instinct (rankings can breed sibling conflict) is real, but a bare on/off is the lazy fix. Lean: a household-level setting that softens rank into something private/personal (see your own progress, not a sibling ladder) rather than deleting it outright — preserves the motivation, removes the podium that starts fights. Needs Scott's actual call before this becomes a decision.
23. **Photo avatars — declined in the literal form, want rerouted.** A raw uploaded photo breaks the locked illustrated style floor (luminosity / inner glow / stylized / jewel-color) and would make every hand-crafted portrait read as set dressing. The underlying want ("I want it to be me / my kid") is legitimate and gets served two ways instead: near-term, expand roster variety (already-parked content lift, zero new system); long-term, a genuine photo→stylized-hero render pipeline that pushes an uploaded face *into* the style floor, reusing the already-scoped alpha-check asset pipeline. Never a bare photo in the circle.

### Validated, not built (confirms existing roadmap — no action needed)
24. **Wall/kiosk mode + kid quest-board-as-earning-surface.** An external tester independently reinvented the wall-tablet walk-up-select-yourself-with-timeout-lock concept, and separately read the adult (owner) Home view as "combined" when the actual kid Home is already a pure quest board. Both are the display/kiosk phase already scoped, arriving right on schedule from an outside brain — treat as validation, not a new todo.
25. **Homeschool / at-home-kids GTM angle + schoolwork-as-quest.** Homeschool families (a lot of them in the tester's network) have kids home most of the day — more household coordination surface, higher daily-habit intensity, which is exactly the moat. Worth remembering for distribution, not building toward now. If schoolwork ever becomes quests, it fits inside the existing multi-step-quest model (expectational work, same shape as chore lists) — not a new gamified module. Membrane holds either way.

### Infrastructure finding (not a feature — a fact that gates a future feature)
26. **No functional service worker — confirmed via airplane-mode test.** The PWA manifest works fine (installs to home screen, launches standalone) but offline load falls through to Safari's native "not connected" error, not a cached app shell. This means PWA web push has a named prerequisite that didn't exist in writing before: **add a service worker + cache the app shell is task zero of the push build**, before any VAPID/OneSignal/sender work — push cannot register without a service worker to attach to. No action needed today; the install tutorial (#14) is unaffected, it rides on the manifest alone.

---

## 7/10 SESSION — suggested opening order

1. **Confirm the retest** for the member-creation fix (kid + adult + bad-PIN, ideally Phaeaz's own cold flow). This is the only unclosed loop from the actual fire.
2. **Engine bundle** (#1, #2, #3) — one prompt, they interlock.
3. **Copy sweep** (#4, #5, #6) — mechanical, cheap, do it in one pass across spec + app.
4. **Campaign bug fix** (#17) — small, isolated, no reason to wait.
5. Everything else burns down by build-order priority as credits allow — Vault work (#7–#10) and Lists (#11–#12) are natural next blocks; security bundle (#19, #20-mitigation, #21) belongs on the pre-distribution runway, not urgent today but shouldn't drift indefinitely either.

Spec update still owed from the 7/03 avatar session (axis method, framing rule, Hall rename, roster size, register-spread-per-class) — fold that in the same housekeeping pass as this list.

---

## Reflection (worth keeping past this hiatus)

The credit crunch didn't slow this down — it removed the option to fix-on-sight, which is what let 26 items get *fully seen* instead of half-noticed-then-patched. Building and seeing are different modes that interfere with each other; a look-don't-touch pass will always out-find a pass where fixing is on the table, because fixing is a distraction dressed as progress. Worth deliberately scheduling a "seeing" pass again even when credits aren't scarce — the constraint just proved the technique works.

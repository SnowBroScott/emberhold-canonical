# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-19 (audit)** — *the P4×L8 tenant-isolation audit was RUN — the mortal-peril item, breached where it was weak, fixed, and re-verified closed on the live glass.* First time the gate got tested instead of reasoned about. Two throwaway households (Alpha, Bravo), real confirmed owner JWTs, every probe fired live against the deployed DB. **The isolation model is verified sound:** all cross-tenant reads/writes, `current_family_id()` integrity, every household-scoped SECURITY DEFINER RPC, the `family_xp` view, and the insert trigger — SECURE, denials shown. **Two breaches found — both grant drift, the recurring disease** — and both closed in one migration, then re-verified by re-firing the exact attacks with legitimate same-family paths confirmed still working. Finding A: `member_spendable()` wasn't family-scoped + quest FK columns accepted foreign-family profiles → cross-household ember read/tamper (now family-scoped + `enforce_quest_family_refs` trigger). Finding B: `families.is_founder` was owner-writable → self-serve paywall bypass (now service-role-only; folded in scanner #7's anon-execute revoke). Teardown (a) done; a small dashboard cascade remains. **The security distribution gate is cleared** — with one honest asterisk: grant-enumeration came back INCONCLUSIVE (the auth seat can't reach the catalog), so "every tested surface secure + both found breaches closed" is the accurate claim, not "grant surface provably clean." That last read is the Lovable SQL-dump method, and it does **not** block distribution. **The project flips from security-blocked to feature-and-polish.**

Last session (prior): **2026-07-19 (day)** — *Lovable's 8 scanner findings triaged end-to-end; P4×L8 reachability solved and the authenticated attack briefed.* Two of the three "Criticals" were our own safeguards holding (false positives). Reachability caveat resolved — Code self-provisions confirmed households via the browser tool. Two build-model decisions logged (the trust-exception for high-stakes attacks; the browser-provisioning rule).

Last session (prior): **2026-07-19 (morning)** — *wall display-mode fine-tune, shipped on the physical iPad.* Avatars grew ~18%; the quest-count two-line wrap killed. Two durable wall-rendering rules logged. Theme-pack monetization hardened into **living-hold ambient themes** — parked.

Last session (prior): **2026-07-18 (evening)** — *avatar transport shipped + a three-item QA burn-down.* 48-avatar roster live, tier-tagged free/founder (16/32), founder gate built and dormant, re-forge run. Render-fallback ("the floor") built at close.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, an ambient wall for the home hold, a live 48-avatar roster with a dormant founder gate — and, as of 07-19, a tenant-isolation model verified sound under live authenticated attack.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, the wall/display mode (v1 own-hold), the tiered avatar system. A complete product loop is live at theemberhold.com.

**The security gate — the thing everything was blocked behind — is cleared.**

> **P4×L8 RAN and the boundary held.** Two households, real JWTs, live attack. Isolation SECURE across every tested surface; two grant-drift breaches found and closed and re-verified. **One non-blocking residual:** grant-enumeration wasn't exhaustively readable from the auth seat — a Lovable SQL-dump read is the difference between "every tested surface secure" (true now) and "grant surface provably complete." Safe to distribute on what's verified; that read makes it airtight. *Caveats carried forward: live-schema-drift is real — both breaches were drift — so the grant read matters, and hand-applied DB changes remain forbidden.*

**The remaining structural gap is activation, not security.**

> **Onboarding ends at setup, not at activation.** A new household lands on a board with no doorway to the first quest. This is now the top structural item before strangers — no longer gated behind security.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **✅ P4×L8 tenant-isolation audit — SECURITY GATE CLEARED (2026-07-19).** Ran as a live authenticated two-household attack; boundary verified sound; two grant-drift breaches (A: cross-tenant ember tamper; B: self-serve founder) found, fixed, re-verified CLOSED. **Residual (non-blocking): the grant-enumeration read** via the Lovable SQL-dump method — table-by-table `authenticated` grant truth, the one slice the auth seat couldn't reach. Do it before wide distribution; it does not block the next builds. | — (was: Distribution / Gate B) |
| **2** | **Pip first-run onboarding screens** + empty-board doorway fix. **Now the top structural blocker — unblocked by the security clear.** | Gate D. Activation. |
| **3** | **Auth email branding + deliverability** | First artifact a stranger receives; lands in spam. Thirteen already received it. |
| **4** | **Founding Guildhall build** (Stripe + webhook + entitlement write). *Note: the paywall it sells is now actually enforceable — the `is_founder` self-grant hole (Finding B) is closed.* | Money. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready; the gate mechanics are built and now secured. | — |

**Next-action fork (Scott's call):** security no longer dictates the order. Honest top candidates: the **grant-dump read** (cheap, closes the last security asterisk), the **auth email** (contained, first-impression), or **Pip onboarding** (the real activation gap). The three security housekeeping items (#5/#6 below, + the grant-dump) are the tidy-up trail behind the audit.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (audit): P4×L8 RUN, BREACHED, FIXED, VERIFIED

*Authenticated two-household browser-provisioned attack against the live DB. Verdict-level record only — attack/exploit specifics live in the Workstream 1 / Code session, never this public repo.*

**Isolation verified sound (SECURE, denials shown live):** cross-tenant reads (families, profiles, quests, rewards, redemptions, events, lists, list_items, activity_log — all return zero foreign rows); cross-tenant writes (INSERT/UPDATE/DELETE into the other household — RLS-denied); `current_family_id()` integrity (no arg/header/body override surface); every household-scoped SECURITY DEFINER RPC (`wall_request_redemption`, `admit`/`deny_pending_member`, `set_profile_pin`, `approve`/`deny_redemption`, `parent_self_redeem`, `get_member_email`→no PII, `get_family_join_code`→own only, `verify_profile_pin`→no cross-tenant oracle, `complete_household_setup`→no cross-join); the `family_xp` view (`security_invoker=true` confirmed); the insert trigger (`enforce_profile_insert_rules` attached live); `system_flags` write (SELECT-only for authenticated — the global paywall can't be flipped by a user).

**Finding A — cross-household ember tamper — FOUND & CLOSED.** `member_spendable()` wasn't family-scoped and quest FK columns (`claimed_by`/`assigned_to`/`approved_by`) accepted foreign-family profiles → a household could read and inflate another's spendable balance, which the victim's own `approve_redemption` trusts. Real breach; mitigated in practice by victim profile UUIDs not being cross-tenant readable. **Fix:** `member_spendable()` scoped to `current_family_id()` (foreign profile → null); new `enforce_quest_family_refs` trigger rejects any of the three FK columns pointing outside the quest's family. **Re-verified:** cross-family `member_spendable` → null (same-family still returns real balances); cross-family FK writes → rejected; legitimate same-family claim/assign/approve → still succeed.

**Finding B — self-serve founder / paywall bypass — FOUND & CLOSED.** `families.is_founder` was writable by an authenticated owner (grant drift; repo intended `UPDATE(name)` only) → any household could self-grant premium. Not cross-tenant (the Bravo-target variant was SECURE). **Fix:** revoke `UPDATE(is_founder)` from `authenticated` (service_role only — the eventual Stripe path); + revoke `anon`/`PUBLIC` EXECUTE on `founder_gate_enabled()` and `my_household_is_founder()` (**folds in scanner finding #7**). **Re-verified:** owner `is_founder` write → denied; owner `name` write → still succeeds (no over-rotation); both founder RPCs as anon → execute denied.

**Fix delivery:** one bundled migration (Lovable, auto-accept OFF), landed as a versioned migration file — not hand-applied (hand-applied drift is what created both breaches).

**Teardown (a) done:** all reachable game data in both households deleted by Code as owner. **Residual → Scott's dashboard cascade** (owner JWT can't reach these): the two throwaway families (Alpha/Bravo) + their 3 auth users, including the orphan `+bravo` unconfirmed user. *(Alpha's family was renamed during a positive-path test — harmless, slated for deletion.)* Identifiers are in Code's `manifest.txt` / the audit chat — deliberately kept out of this public repo.

**Open slice (non-blocking): grant-enumeration — INCONCLUSIVE.** The authenticated data-API seat exposes only `public`/`graphql_public`; `information_schema`/`pg_proc` are unreachable, so table-by-table grant truth can't be read from an owner seat. → Lovable SQL-dump method (the 07-16 extract-and-judge). Both found breaches were grant drift, so this is the honest last step to "provably clean." Does not block distribution.

---

## 🟢 SECURITY TRIAGE — Lovable's 8 scanner findings — DONE 2026-07-19 (day); statuses updated post-audit

*Verdict-level only. Mechanism lives in the Code session, never here.*

**False positives — safeguards held (vindicated):**
- ✅ **"Forgot PIN" takeover (was CRITICAL) — FALSE POSITIVE.** Reset RPC is parent-role + same-household gated; anon denied. *(Minor: confirm() copy claims "only the account owner" — inaccurate; UX-copy fix.)*
- ✅ **Join-code → Parent admin (was CRITICAL) — FALSE POSITIVE.** Join hardcodes `role='kid'/status='pending'`; real tier set by parent-gated `admit_pending_member`. The 07-15 anti-escalation wiring, confirmed.
- ✅ **Broad SECURITY DEFINER surface (#8) — CLOSED.** Sensitive definer RPCs anon-denied/internally guarded (re-confirmed live in the audit).

**Real — now resolved or queued:**
- ✅ **`founder_gate_enabled()` + `my_household_is_founder()` anon-executable (#7) — FIXED.** Folded into the P4×L8 fix bundle (anon/PUBLIC execute revoked). Re-verified denied.
- ⬜ **Adult PIN plaintext in `localStorage` during signup (#5) — CONFIRMED, QUEUED. CREDIT-FREE.** PIN persists plaintext across the email-confirmation window. Fix: collect it in post-confirmation finish-setup, don't persist client-side. Frontend-only → Code commit, no Lovable credit. Lowest severity; the only free one.
- ⬜ **Kids read `adults_only` reward names/costs (#6) — CONFIRMED, DEFERRED (on purpose).** Rewards SELECT filters by family only; audience hiding is client-side. **Held, not reflex-patched:** the real fix tangles with the parked own-session-vs-per-member-auth decision (in the shared-session model a server-side audience predicate still won't isolate a device-kid). Fix it *with* that decision, not before. See parking-lot.

**Known-accepted boundary (re-confirmed by the audit):**
- 🔵 **Tier boundary is frontend-only in the shared-session model (#4) — KNOWN-ACCEPTED.** A device-kid rides the owner's ambient parent JWT. This is the **walk-up trust boundary** (recon 07-15) — **intra-household, not cross-tenant** (the audit confirmed `current_family_id()` derives server-side and does not cross the tenant line). The revisit-trigger (P4×L8) fired; the own-session-vs-per-member-auth fork it raises is parked.

**Quest UPDATE / trigger (#3):** cross-tenant facet is SECURE (RLS blocks foreign quest writes before any trigger matters); the intra-household `a_enforce_quest_update_authority` trigger governs kid-vs-parent, which is the accepted walk-up boundary above. No cross-tenant exposure.

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock` (bun not in the recon env). Run `bun audit` to reproduce the scanner's "1 vuln." Low priority.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-19 (morning)

**Wall display-mode fine-tune — verified on the physical glass.**
- ✅ **"Who's on the Hearth" avatars enlarged** (~18% of tile width; ring offset + padding tightened so size isn't re-consumed by halo/padding).
- ✅ **Quest-count line wrap killed.** Root cause: `whitespace-nowrap` lost across the flex-item boundary; iPad Safari broke the internal space. Fix: NBSP between label words + `whitespace-nowrap` on both inner spans + `flex w-full min-w-0`/`items-baseline`.
- **Two durable wall-rendering rules captured** (decisions.md): NBSP-not-nowrap for unbreakable wall text; the halo/padding tax on any wall avatar resize.

---

## 🟢 SHIPPED / RESOLVED — 2026-07-18 (evening)

**Avatar transport — shipped end-to-end.** 48-roster into `src/assets/avatars/`, committed, synced. Tier-tagged free/founder (16/32). `families.is_founder` added (default false; *now service-role-write-only after the P4×L8 fix*). Global gate as a DB value (`system_flags.founder_gate_enabled`, seeded false) — **flip is a one-line UPDATE, not a build.** Picker shows all 48; founder tiles lock only when gate ON and household not founder. Re-forge: 28/28 invalidated, Scott ran it live. Roster art verified on the glass (`keep-10` lowest-quality, running with it).

**QA burn-down — three shipped, verified:** Ranks per-member completed-quest expand (phone + wall); calendar month view full visible month (phone + wall); wall "hero" vocab leak killed. **QA #5 reclassified** as cross-hold super-admin / tier-2 support → distribution-era.

---

## 🟡 PENDING VERIFY

- 🟡 **Avatar render fallback ("the floor")** — glass test: pull up a not-yet-re-forged member, confirm a clean placeholder not a raw key, check the **wall** specifically.
- 🟡 **Founder tier-tag verification needs a gate-on moment.** Briefly flip gate ON, confirm exactly **32 lock / 16 open**, flip back OFF. A mis-tag is a silent revenue leak or locked-out freebie.
- 🟡 **Routing fix (`28ab40d`)** — sign out from a pending waiting screen, sign back in, confirm you land on the waiting screen and auto-advance on admission.
- 🟡 **Recurrence chip legibility.** `RECURRENCE_LABEL` enrichment scoped, skipped (exceeds one line). Parking-lot NEXT.
- 🟡 **Lists collapsible sections** (`fb6aa99`) — landed, not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — whole confirmation flow on ONE device, cold. Still unproven.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify persisted.
- 🟡 **Wall adult-verified turn-in commit** (built 07-18 day). Glass test: approve → PIN → wrong/kid mints nothing → correct session-owner PIN commits + balance moves → `approved_by` lands as session-owner.
- 🟡 **Wall — full end-to-end membrane loop** — claim → turn-in → approve on a separate device; confirm the wall never exposed a commit control it shouldn't.

---

## ⬜ OPEN FINDINGS — from the avatar session

- ⬜ **Re-forge reach across the 13 households (real defect, later fix).** Re-forge only prompts the member who logs in; others render the fallback placeholder until an adult manually fixes each. A calm, later decision — matters when re-forge reaches the 13. The floor defuses the ugliness meanwhile.

---

## ⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)

- ⬜ **Feed verb drift.** A newly-posted quest shows "New quest: …"; the 7/12 bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align.

---

## 🟢 SHIPPED — 2026-07-18 (day)

**Doc-protocol repair + catch-up committed.** 07-17's over-correction reconciled as never-canon; model restored; six 07-17 decisions committed; sync-to-origin rule folded into `session-protocol`.

**SnowDad Vault spendable = 0 — cleaned (data, not code).** Debit-side test cruft reconciled to 106. **Two findings logged:** `parent_self_redeem` doesn't write `activity_log` (feed blind spot, NOTED); `member_spendable` silently clamps overdraw to 0 (NOTED — was a P4×L8 input; the audit's Finding A addressed the family-scope facet, the clamp-hiding facet remains a NOTED UX/ledger item).

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Grant-enumeration read (the one open P4×L8 slice) — Lovable SQL-dump method.** Table-by-table `authenticated` grant truth. Non-blocking; the honest last step to "grant surface provably clean." Both found breaches were grant drift, so this is worth doing before wide distribution. ⚠️ *Findings detail stays in the Code/Workstream session, not this repo.*
- ⬜ **PIN-plaintext-in-localStorage (#5)** — credit-free Code frontend commit.
- ⬜ **Rewards audience (#6)** — deferred, fix *with* the own-session-vs-per-member-auth decision (parked).
- ⬜ **Auth email branding + deliverability.** Sender `no-reply@auth.lovable.cloud`; lands in spam. Custom sender: DNS at Porkbun + SMTP (Resend). ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Service worker + app-shell cache** — makes "installable PWA" true.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.** The two P4×L8 throwaway families (Alpha/Bravo) + their 3 auth users (incl. orphan `+bravo`) — Scott's dashboard cascade. Plus older cruft: `QA Parent`/`QA Joiner`/`QA Verify Hold`, `+ca@gmail.com`/"Testies". **Pattern: deleting a quest does not clean up redemptions it funded — reconcile, don't just delete.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Pip first-run onboarding screens** + empty-board doorway fix. **No longer gated behind security — now the top structural blocker.**
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`. Doubly load-bearing (the wall's Vault is affordable-only because of this). Build phone-first. On the wall it's an on-behalf write (`wall_toggle_favorite`).
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Feed verb drift** · **Quick Add default EXPANDED on empty board** · **`TITLE` → `QUEST TITLE`** on create · **Lists "5 OPEN · 348 DONE"** fossil counter · **Pip help discoverability** · **Pip install tutorial** · **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **Multi-day calendar events** (QA #7) · **Calendar alerts** (QA #6 — push-vs-in-app; push fenced) · **List suggestions in onboarding** · **Wall ticker speed** · **Wall calendar event-pill member color: dot-vs-full-tint**.

---

## 🅿️ PARKED

See `parking-lot.md`. **Living-hold theme packs — monetization SKU #2** (07-19) · **QA #5 super-admin / tier-2 support role** (07-18) · **Re-forge reach across the 13** (07-18) · **Own-session vs per-member-auth** (the fork the walk-up boundary raises; rewards-audience #6 waits on it) · Favorites on the wall · role-label retirement ("Parent/Kid," esp. "Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor · flat/peer holds · recurrence chip legibility · the "how Scott & jAIne work" collaboration profile.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15, extended 07-17, confirmed 07-19)

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT, so economic RPCs (gated on `has_role(parent)`) are satisfied by that session; the PIN is the only kid/parent line and it's client-side. **This is intra-household, not cross-tenant** — the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()` and does not cross the tenant boundary. Accepted for household use. The own-session-vs-per-member-auth fork is parked. See `decisions.md`.

---

## 🔵 THE BUILD MODEL — holding

- **Code's lane is TEXT** — anything verifiable by reading. Visual success criteria are Scott's. **Live DB state is Lovable's.**
- **The docs are not the live codebase — and can diverge silently.** Session-open "verify status against reality" is the bilge pump. Code recon syncs to origin/main first (LOCKED 07-17).
- **Hand-applied DB changes are forbidden — everything lands as a migration file.** Both P4×L8 breaches were hand-applied grant drift. Live-schema-drift is now a 5×-observed pattern.
- **High-stakes live-DB audits get a directive, precise roadway — not "floor + latitude."** (07-19.) Mandate the tool, sequence, surface, and report; latitude only where jAIne is blind (live-schema exploit execution).
- **Authenticated live-DB audit briefs MUST mandate the browser tool for account provisioning.** (07-19.) Reachability was never a stack limit.
- **Fix → re-fire the exact attack → verify denied on the live artifact → then teardown.** Never ship a security fix on a green checkmark. (07-19, applied.)
- **Model routing:** Haiku (recon/mechanical) · Sonnet (build + diagnosis) · **Opus (the tenant-isolation audit).**
- **One writer at a time.** Data-layer / live-DB → Lovable; frontend → Code.

---

## ✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)

Finding #1 (join-code bypass) fully closed. Enum `profile_status = ('active','pending')`; `current_family_id()` returns NULL for non-active → every family-scoped RLS policy denies a pending member. *(Re-confirmed 07-19 twice: the scanner's "join-code → parent" claim is a false positive against this wiring, and the audit found no cross-tenant path through the join flow.)*

---

## 🟢 SHIPPED — 2026-07-16 (roster grant fix + routing)

Roster "no members" root-caused to zero Data-API grants for `authenticated` (a live-schema-drift instance); grants restored across 14 tables. Pending→admission routing fixed (`28ab40d`, 🟡 pending live repro). *(Also the session that proved Code can browser-provision confirmed test households end-to-end — the reachability precedent the P4×L8 attack relied on.)*

## 🟢 SHIPPED — 2026-07-15

Live privilege-escalation in `complete_household_setup` fixed. Admit/deny enum crash fixed. `recurrence_day` prod crash fixed (`172a07f`). Pending-member roster leak fixed (`b31c92c`).

## ✅ SHIPPED — 2026-07-14

Admit-on-approval data layer. Hub open-bounty count unified. Recurrence reworked to fixed calendar anchors.

## ✅ SHIPPED — 2026-07-12

Claude Code proven as a build lane. Auth hardening (email confirmation, holding-state signup, password reset, min length 6→8). Hold Alpha built (the P4×L8 instrument). 13 real accounts confirmed.

## ✅ SHIPPED — 2026-07-11

Engine daily respawn verified. Vocabulary cleanup. Safe-area inset fix. Avatar roster regenerated (48 characters, 19 sheets). Lovable ↔ GitHub sync connected.

## ✅ SHIPPED — 2026-07-10

Engine auto-approval + daily board fix + edit-form parity. XP killed. Vault kid/adult/couples-rail. Quest audience filter. Over-broad anon EXECUTE grant revoked. Docs migrated; `north-star.md` created.

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Rename confirmed still present on the live member editor 2026-07-18 — queued for the Haiku sweep.*
- **2026-06-29 → 06-26** — Lists v1, invite/deep-link, notifications, PIN recovery, activity feed spine, monthly recurrence + audience, Campaigns, Calendar, Briefing, the Vault, PIN, Quest Log.

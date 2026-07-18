Status
Where the build is and what's left. The single status board.

Last session: 2026-07-16 — came in to close pending-verifies; the discipline earned its rent inside five minutes. Session opened by eyeballing b31c92c on the live app per the "log lies until a human uses it" rule — and the eyeball caught a roster regression on the real family board (The W Drapers): board Roster and switch picker showed "No one in the household yet" while Ranks correctly showed all four members. Ran it as a read-only diagnosis (Code, auto-off, Sonnet): refuted the working-tree theory, confirmed the data is clean (all four profiles status='active' via a live read), and confirmed jAIne's hypothesized NULL security asymmetry does NOT exist (both paths fail-closed). Real root cause found by Lovable, live: the public tables carried zero Data-API grants for authenticated — every signed-in query died at the permission layer before RLS. Grants restored across all 14 tables; roster now populates live. The fourth live-schema-drift instance — the defect was in DB state no migration file describes. Separately: shipped the pending-member routing fix (28ab40d) from the prior turn's brief, and resolved "all quiet at the hold" as working-as-intended, not a bug. Through-line: the session-open "eyeball it first" discipline caught a live bug on the real family board before any new work started, and trust-the-lane (Lovable had live-DB eyes Code and jAIne didn't) found what neither could.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

Where the platform is
Structurally complete. Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery, the full admit-on-approval join flow, and now a hardened pending→admission routing path — a complete product loop is live at theemberhold.com.

What's missing is not a module. Two structural gaps:

Security — one gate left before distribution. #5 and #8 patched by a June migration; #6 (kid self-approval) CLOSED (blocked by a_enforce_quest_update_authority); #1 (join-code bypass, CRITICAL) fully CLOSED — data layer, frontend, and live-DB verification, plus a separate escalation the audit caught. The remaining gate is the P4×L8 tenant-isolation audit (the mortal-peril item). Caveats carried forward: the 07-14 data layer was verified secure by the live audit, but functional bugs still shipped inside it — verified-secure ≠ verified-functional. And 2026-07-16 added a fourth live-schema-drift instance (a missing Data-API grant) — the migration files do not fully describe the live DB, so the audit reads the live grant surface, not the repo.

Onboarding ends at setup, not at activation. A new household lands on a board with no doorway to the first quest. Gated behind distribution. (Distinct from the "all quiet" line, which is correct — see below.)

See north-star.md for the gate ladder.

🔴 THE CRITICAL PATH
#	Item	Blocks
1	P4×L8 — the tenant-isolation audit (Workstream 1). Opus, auto-accept OFF. The mortal-peril item and the last security gate before distribution. Hold Alpha exists — run it as an attack. Free inputs banked: the 07-15 live-catalog dump + the 25 Supabase linter warnings + (new 07-16) the Data-API grant surface (a hole that silently DENIED just surfaced; confirm no sibling hole silently ALLOWS). First: confirm the adversarial harness can even reach Lovable Cloud's DB from outside (see parking-lot open decision) — if not, the audit degrades to policy-reading.	Distribution. Gate B.
2	Pip first-run onboarding screens + empty-board doorway fix	Gate D. Activation. Day-8 retention. Gated behind #1 — it's for strangers, and strangers come after the door locks.
3	Auth email branding + deliverability	First artifact a stranger receives; lands in spam. Thirteen people have already received it.
4	Avatar transport — generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain. Tabled by Scott — on the board, not a priority; do not push it, note it.	The Guildhall, the delight layer
5	Founding Guildhall build (Stripe + webhook + entitlement)	Money
Next-action fork (Scott's call): the honest sequence is P4×L8 first — it's the gate, and tutorial/display/avatars all sit downstream of the distribution it unblocks. But display/wall mode is the one delight item that does not need the gate open — ambient presence for Scott's own household — so it's the defensible morale-build to pull forward if a "fun" session is wanted. Or the contained win: the auth email — lower stakes, no Opus window, real users on the wrong end of it right now. See parking-lot for display mode's dependencies.

🟡 PENDING VERIFY
🟡 Routing fix (28ab40d) — live repro. Sign out from a pending account's waiting screen, sign back in, confirm you land on the waiting screen (NOT the setup form) with a clean single-entry history; confirm the waiting screen auto-advances into the app the moment a parent admits (no manual refresh/relogin). Code traced and closed it mechanically and confirmed the backend seam (a pending user re-calling complete_household_setup hits the reconcile branch, which ignores caller params and can't escalate or hop households — no P4×L8 finding). A green build isn't confirmation; needs the human repro.
🟡 Recurrence chip legibility. Anchors are invisible — a kid sees "MONTHLY" with no hint it means the 1st. RECURRENCE_LABEL enrichment ("Weekly · Mon" / "Monthly · 1st") scoped and skipped (exceeds one line). Parking-lot NEXT.
🟡 Lists collapsible sections (fb6aa99) — landed, not yet exercised across a full session.
🟡 Cross-device / phone-only signup — whole confirmation flow on ONE device, cold. Still unproven on fixed code.
🟡 Phaeaz cold-account retest — open since the hiatus.
🟡 Min password length 6→8; re-auth on password change ON — verify persisted.
⬜ ONE BOARD BUG STILL OPEN (surfaced 2026-07-14)
⬜ Feed verb drift. A newly-posted quest shows "New quest: …"; the 7/12 vocabulary bundle standardized on "QUEST POSTED." A surface that bundle didn't reach. Verify and align. (Moved to parking-lot polish burn-down; kept here as the last of the pair.)
Resolved 2026-07-16 — the "empty-board eulogy" is NOT a bug. "ALL QUIET AT THE HOLD" reports the adult's action queue ("nothing needs your seal"), not household inactivity. Open bounties are posted work waiting for a kid; a logged completion needs no seal. On the boards where it was flagged, the queue was genuinely empty, so the line was true. See decisions.md. (The separate new-household doorway gap — an empty board needs "post your first quest with +" — is real, LOCKED 2026-07-12, and gated behind distribution. Different problem, same screen.)

✅ SHIPPED / RESOLVED — 2026-07-16
Roster / switch-picker "no members" — root-caused & fixed (Lovable, live)

✅ Board Roster and profile switch picker showed empty for The W Drapers while Ranks showed all four. Root cause: public tables had zero Data-API grants for authenticated — every signed-in query rejected with permission denied for table profiles BEFORE RLS evaluated. Ranks survived because it reads the family_xp VIEW (predates the hole). Fixed: SELECT/INSERT/UPDATE/DELETE restored to authenticated, ALL to service_role, across all 14 tables; RLS still gates rows. Roster + picker now populate live with all four members.
✅ Fourth live-schema-drift instance — defect lived in live DB state no migration describes (like recurrence_day, the monthly enum). Named rule logged: code provably clean + still broken = suspect drift FIRST. See decisions.md.
✅ Diagnosis discipline held: three theories (NULL legacy rows / backfill drift / wrong-session) all died against a read-only Code diagnosis + a live data read + Scott's screenshots. b31c92c confirmed innocent of this bug — active rows pass an active filter.
⬜ P4×L8 input (new): the grant surface is now a named audit line item — a hole that DENIED surfaced; confirm no sibling that ALLOWS. current_family_id() NULL-handling checked and confirmed fail-closed (no asymmetry). See decisions.md.
Pending→admission routing — fixed (28ab40d), 🟡 pending live repro

✅ A pending member on re-login was landing on the create/join setup form; the waiting screen was only reachable via browser Back ([waiting] → [setup] history). Cause: signIn() couldn't distinguish "no profile yet" from "pending admission" (the pending member's own row is RLS-hidden from themselves), so it fell to finish-setup; compounded by push-not-replace redirects.
✅ Fixed: signIn() checks get_pending_membership() before falling to setup; a stage-level guard bounces off finish-setup to /board (replace) if the DB disagrees; both auth redirects now use replace; the waiting screen polls get_pending_membership() (~8s) and auto-advances via router.invalidate() on admission — killing the sign-out/sign-in loop. Join-flow toast now states the two-step gate ("Email verified. Now a parent needs to admit you…").
✅ Security seam checked, nothing to log: a pending user re-calling complete_household_setup hits the reconcile branch, which ignores caller params — no escalation, no household hop.
"All quiet at the hold" — resolved as working-as-intended. See the board-bug note above and decisions.md.

⬜ OUTSTANDING — security & distribution
⬜ P4×L8 tenant-isolation audit — see critical path #1. Confirm harness reachability first (parking-lot). Banked inputs: 07-15 live-catalog dump · 25 linter warnings · the Data-API grant surface (07-16).
⬜ Auth email branding + deliverability. Sender no-reply@auth.lovable.cloud; display name Family-Quest-Board (scaffolding, exists nowhere in the product). Lands in spam. Templates in Lovable Cloud → Emails. Custom sender: DNS at Porkbun (free) + SMTP (Resend free tier). ⚠️ Inspect any NS-record request before pasting — delegation ≠ a TXT record.
⬜ Service worker + app-shell cache — no functional SW; makes "installable PWA" true instead of aspirational.
⬜ Backup posture. Backend is Lovable Cloud; backups/PITR/export are Lovable's to grant. No direct Supabase access — all backend routes through Lovable. Code has a Git backup; the data has none. Gate B blocker. (See parking-lot open decision.)
⬜ Prod test-object cleanup. QA Parent / QA Joiner / household QA Verify Hold — created on the production backend during 07-15 admit-flow testing (local dev points at the same DB as theemberhold.com — see parking-lot staging-DB decision). Plus older cruft: Testi, testy, Daily test, iopuyiouh, and orphaned auth users scottydawg@gmail.com, scott.draper83+alpha@gmail.com. (2026-07-16 may have added a +ca@gmail.com pending/test account and the "Testies"/"Testicular" test hold during the routing repro — sweep those too.) Data, not code.
⬜ OUTSTANDING — ship-blocking debt
⬜ Avatar transport. Tabled by Scott. Full scope in parking-lot: hand-cut roster, file drop (Haiku), rewrite HERO_AVATARS, land Feast→Hall, retire avatar-review.tsx, invalidate stale hero: picks (the re-forge migration), re-lock the free/paid split (the 16/24 math predates the 48-roster). Do not push; surface when Scott calls it.
⬜ Pip first-run onboarding screens + the empty-board doorway fix. Gated behind P4×L8.
⬜ Vault favorites → real per-profile persistence — currently localStorage.
⬜ Quality — a rating with no consumer. Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility open. Binds to the weekly recap.
⬜ OUTSTANDING — polish
⬜ Feed verb drift (see board bug above) · Quick Add default EXPANDED on an empty board · TITLE → QUEST TITLE on create · Lists "5 OPEN · 348 DONE" fossil counter · Pip help discoverability · Pip install tutorial · Reward scarcity limits · Cheap Dim-tier starter reward · Yearly/monthly event recurrence · List suggestions in onboarding.
🅿️ PARKED
See parking-lot.md. Display/wall mode (the defensible morale pull-forward) · role-label retirement — "Parent/Kid," esp. "Kid" (new 07-16) · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor · flat/peer holds · recurrence chip legibility.

🔴 WALK-UP TRUST BOUNDARY — recon on record (2026-07-15)
Switching into a PIN-less adult profile hard-gates correctly (session never fires ahead of PIN-set success); Cancel returns to the picker (no bypass). But the "active member" switch is cosmetic, not a security boundary — on a shared device signed into a parent's account, physical possession = parent authority, so a kid holding the unlocked device can set/reset an adult PIN (the server correctly sees a parent session). This is the walk-up kiosk model working as designed, consistent with the 2026-07-10 kid-auth DECLINED decision. Accepted for household use; logged as a live input to the P4×L8 audit for the distribution-grade version. See decisions.md (NOTED, revisit trigger = P4×L8).

🔵 THE BUILD MODEL — holding, stress-tested again
Claude Code diagnosis + Lovable live-DB fix, both proven. 2026-07-16 exercised the full split cleanly: Code ran a read-only diagnosis (auto-off, Sonnet), refused to guess the data it couldn't see, and handed back the one query that would settle it — then Lovable, the only lane with live-DB eyes, found the grant hole neither Code nor jAIne could see from the repo. The lesson held both ways: the repo is not the live DB, and trust-the-lane means letting the lane with the right senses swing.

Code's lane is TEXT — anything verifiable by reading. Visual success criteria are Scott's. Live DB state is Lovable's — the repo doesn't contain it.
The live-DB audit method (Lovable extracts/reads read-only, an independent agent judges) is the standard for data-layer verification. See decisions.md.
Model routing: Haiku (recon/greps/mechanical) · Sonnet (build + diagnosis with latitude) · Opus (the tenant-isolation audit only).
One writer at a time. Data-layer / live-DB → Lovable; frontend → Code.
Still unproven: whether Lovable runs an externally-pushed migration (the avatar hero: reset remains the cheap first test — tabled).
✅ ADMIT-ON-APPROVAL — SHIPPED & VERIFIED (2026-07-15)
Finding #1 (join-code bypass) is fully closed: data layer, frontend, live-DB verification, and one adjacent escalation, all done.

Data layer (07-14, Lovable): enum profile_status = ('active','pending'); current_family_id() returns NULL for any non-active profile, so every family-scoped RLS policy denies a pending member by construction — one fail-closed chokepoint. Join creates a pending profile, role='kid' placeholder, self-selected role stored as advisory requested_role, no user_roles row at join. enforce_profile_role_change blocks non-parents from writing status/role.

The RPC contract: get_pending_membership() (pending self, waiting screen) · list_pending_members() (parent) · admit_pending_member(_profile_id, _confirmed_role) (parent — confirmed role authoritative, request advisory) · deny_pending_member(_profile_id) (parent — deletes pending profile, auth user survives to retry).

✅ Verified live (07-15): NULL-denial total across all 47 policies; trigger coverage sound; all four RPCs self-check parent + family scope; cross-household admit blocked cold. Method: Lovable dumped the live catalog read-only, jAIne judged independently.

✅ Frontend shipped (08229d4): ZonePending in Briefing.tsx extended — parent-gated queue over list_pending_members(), Admit opens a role confirmation that defaults to KID (requested role shown as context only), Deny clears the row. Pending user sees a themed "waiting for admission" screen; denied-user routing fixed. (2026-07-16 hardened the pending re-login path on top of this — see above.)

✅ Admit loop verified end-to-end by Scott: admitted a real pending member, kid-default held on an Adult request, waiting screen resolved, roster updated. Shipped — a human ran the loop.

🟢 SHIPPED — 2026-07-15 (the session's haul)
Live privilege-escalation in complete_household_setup — found & fixed. Reconcile branch trusted caller-supplied _role → an active kid could mint a parent user_roles row. Fixed: reconcile sources role from the trigger-protected profiles.role. See decisions.md.

Admit/deny enum crash — found & fixed. admit_pending_member / deny_pending_member inserted significance = 'notable'; enum only permits 'routine' | 'milestone'. Fixed to 'milestone'. Shipped inside the 07-14 data layer the audit had passed — verified-secure ≠ verified-functional.

recurrence_day prod crash — found (real user) & fixed (172a07f). Quest creation failed in prod ("Could not find the 'recurrence_day' column") — core loop down. The 07-14 frontend removal had been stashed, never committed. Applied the stash, committed, verified from outside, Scott reproduced-then-couldn't.

Pending-member roster leak + switch-target — found (Scott) & fixed (b31c92c). Pending members rendered in the roster and switch picker. useFamilyMembers now selects status and filters status='active'. Verified from outside.

🟡 (07-15 carry) — nothing outstanding; all four haul items verified.
⬜ TWO BOARD BUGS (surfaced 2026-07-14) — now ONE
empty-board eulogy — RESOLVED 07-16 (working-as-intended). See above.
⬜ Feed verb drift — still open. See the open-bug note above.
✅ SHIPPED — 2026-07-14
Admit-on-approval data layer (verified 07-15). Hub open-bounty count now matches the board via one shared predicate isOpenBountyVisible(). Recurrence reworked to fixed calendar anchors (weekly=Monday, monthly=the 1st, daily=tomorrow); recurrence_day dropped from DB/trigger — frontend removal completed 07-15 (172a07f).

✅ SHIPPED — 2026-07-12 (day)
Claude Code proven as a build lane (feed vocabulary bundle c9069a6; vault.tsx dedup 7132605; Lists collapsible fb6aa99). Auth: email confirmation required, holding-state signup, password reset verified, min length 6→8. Hold Alpha built (second isolated household — the P4×L8 instrument). 13 real accounts confirmed, not four.

✅ SHIPPED — 2026-07-11
Engine — daily respawn verified; lifecycle verified end-to-end. Vocabulary — OPEN BOUNTY chip killed; XP eliminated from install prompt. Render — safe-area inset fix. Avatars — roster regenerated (48 characters, 19 sheets, ~600px each). Claude Code stood up; Lovable ↔ GitHub sync connected — the codebase's first backup.

✅ SHIPPED — 2026-07-10
Engine — adult auto-approval · daily board fix · edit-form parity. Copy — XP killed. Vault kid view (dual-mode) + adult view (three zones) + couples rail (reward.audience). Quest audience filter + badges. Security — over-broad anon EXECUTE grant revoked. Docs migrated to this repo; north-star.md created.

✅ SHIPPED — earlier (foundation)
2026-07-03 — Avatar Overhaul designed (Feast→Hall). Rename never landed in code — still outstanding via avatar transport.
2026-06-29 → 06-26 — Lists v1, invite/deep-link, notifications, PIN recovery, collapsible picker · activity feed spine · monthly recurrence + audience · Campaigns, Calendar, Briefing · the Vault, PIN, Quest Log.

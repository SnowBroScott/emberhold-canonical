# Decisions
**Append-only.** Newest at top. A decision here is design truth; the *implications* get folded into `master-spec.md`.

**Status tiers:** `LOCKED` (decided, don't relitigate) · `NOTED` (observed, informs future calls) · `DRAFT` (leaning, pending verification) · `DECLINED` (considered and rejected — recorded so it stays rejected)

**Format:**
```
DECISION: [what was decided]
DATE: [date]
WHY: [the reasoning, including rejected alternatives]
REPLACES: [what this supersedes, or: Nothing — new decision]
STATUS: [LOCKED / DRAFT / NOTED / DECLINED]
```

---

DECISION: High-stakes live-DB security audits get a directive, precise roadway — the standing "brief the floor + intent, leave latitude" posture is suspended for them.
DATE: 2026-07-19
WHY: Today's triage instance had every capability to run the authenticated P4×L8 attack — a 07-16 instance self-provisioned two confirmed households via the browser tool and ran admit/deny across both. Instead it worked from a bare curl/anon seat, hit email-confirmation, and self-declared "degraded to policy-reading" — a false limit presented as a stack constraint. The failure was not capability; it was an unsanctioned path chosen under latitude, on the single most expensive item on the board. The fix is to remove the fork (mandate the tool, the sequence, the attack surface, the report format), not to add trust. Latitude survives only where jAIne is blind and cannot specify — the exploit SQL against a live schema jAIne can't see. Rejected: trusting Code to find the right path itself (just demonstrated to fail here); jAIne authoring the attack SQL (blind to live schema, would get columns wrong). Scope: this is an exception for high-stakes attacks, NOT a reversal of trust-the-lane / don't-over-specify for normal build work.
REPLACES: Nothing — scopes an exception to the standing posture; does not overturn it.
STATUS: proposed LOCKED

---

DECISION: An authenticated live-DB audit brief MUST explicitly mandate the browser tool for account provisioning. Harness reachability to the live DB is solved and was never a stack limit.
DATE: 2026-07-19
WHY: A fresh Code instance, left to choose, defaults to a bare curl/anon seat, hits mailer_autoconfirm=false, and concludes "no mailbox → degraded to policy-reading." That conclusion is wrong: a 07-16 instance drove the real signup UI, pulled the confirmation link from the email in-tab, captured a session, grabbed the join code, and ran admit/deny across two accounts. The requirement is to instruct the path, not assume it — the instance will otherwise walk past it. This retires the long-standing open caveat that the P4×L8 audit might "degrade to policy-reading" for lack of reach. Rejected: pre-provisioning the two holds by hand and handing Code credentials — cleaner teardown, but unnecessary now that self-provisioning is proven, and it adds Scott labor for no security gain (cleanup is cheap).
REPLACES: The standing status/parking-lot open question "confirm the adversarial harness can reach Lovable Cloud's DB from outside — if not, the audit degrades to policy-reading."
STATUS: proposed NOTED

---
DECISION: Founder avatar gate — DB-value gate + household entitlement flag, reusing the Guildhall seam
DATE: 2026-07-18
WHY: Building a bespoke gating system was unnecessary — the Founding Guildhall entitlement flag was always going to exist, so the gate reads it instead of inventing a parallel one. Mechanism: a global gate stored as a flippable DB value (system_flags.founder_gate_enabled, seeded false, read via founder_gate_enabled()) + a per-household entitlement (families.is_founder, boolean, default false, read via my_household_is_founder()). Picker rule: an avatar is selectable if tier=free OR gate OFF OR household is_founder. Gate is OFF now, so everything's selectable and nothing renders locked. Household-level only, never per-kid (honors the seam rule). Cosmetic-only (membrane-safe — never touches embers/quests/approvals). Decouples from Stripe: the flag exists now; the checkout that writes it is a later build. The flip later = two one-line data changes (grandfather existing holds to is_founder=true, then set the gate true), not a build session. Rejected: (a) a code-constant gate — would need a redeploy to flip, and Lovable chose the DB-value approach which is strictly better; (b) any per-member gating — violates the household-only seam rule; (c) a separate "founder catalog" — a WHERE clause on tier beats a second system.
REPLACES: The stale "16 free / 24 paid" split arithmetic (locked against a 44-roster)
STATUS: [proposed LOCKED]

---

DECISION: Free/founder avatar split re-locked at 16/32
DATE: 2026-07-18
WHY: The old "16 free / 24 paid" predated the 48-roster and was stale arithmetic. Scott set the new split by hand — sorting the 48 cut, slot-named files into Roster Free (16) and Roster Founders (32), a clean 4-free + 8-founder per class across forge/garden/keep/hall. The folders were the casting; the split is a Scott product decision closed by his action, never handed to an agent. Encoded in the catalog as explicit free/founder tier tags per file. Rejected: leaving the split to an agent as part of transport (the split is taste, not mechanics — it stays Scott's).
REPLACES: "16 free / 24 paid" (stale, 44-roster era)
STATUS: [proposed LOCKED]

---

DECISION: QA "#5 hold admin role" reclassified as a distribution-era super-admin / tier-2 support role
DATE: 2026-07-18
WHY: Raised as a home-hold QA item; on clarification it means someone who can reach into a household to manage members/events/feed — cross-hold support authority. That's cross-tenant and only needed once strangers' holds exist to support, so it belongs on the distribution ladder near Gate C–E, not in a home-hold QA pass. It is NOT a bug and NOT a quick fix. Residual ambiguity flagged and left open in parking-lot: (a) an in-hold admin tier above parent vs (b) a cross-hold super-admin are different builds — Scott to bring concrete examples before it's scoped. Rejected: treating it as a QA-session one-liner (it's a workstream), and building it now (it's post-strangers, gated behind the P4×L8 distribution gate).
REPLACES: Nothing — reclassification of an open QA item
STATUS: [proposed NOTED / PARKED]
---
DECISION: Wall commit is ADULT-VERIFY, not identity-verify. An adult present at the wall commits a quest turn-in (the mint) behind a server-verified adult PIN that proves a committer is PRESENT, not WHICH adult. approved_by records the session-owner adult. Provable per-adult attribution is deferred to the P4×L8 commit-attribution hardening.
DATE: 2026-07-18
WHY: Walked back from an identity-bound design (tap SnowDad → SnowDad's own PIN → mismatch fails → decided_by = the proven adult) after the recon showed identity-bound is a DATA-LAYER build, not a frontend one. The recon findings that forced it: verify_profile_pin(_profile_id, _pin) is already SERVER-SIDE and IDENTITY-SCOPED (bcrypt, hash never leaves the DB) — so authentication was ready. But ATTRIBUTION is not: approve_redemption hardcodes decided_by = auth.uid() (can only ever record the session owner, never a different proven adult), and quest-approval is a direct client UPDATE where approved_by is CLIENT-ASSERTED with no server-side tie to any proof event — the server checks only that the real session has_role('parent'). Making decided_by/approved_by provably equal a PIN-proven adult therefore needs new actor+PIN RPCs with the verify INSIDE the commit — Lovable's lane, a security surface, and it touches the phone's approval path too. That build only exists to make the ledger prove WHICH adult authorized. The moment we stop asking it to prove that, the build evaporates. And the actual threat on a shared wall is a KID self-committing (tapping their own turn-in and minting) — which adult-verify FULLY stops, because kids have no PIN. Identity-verify was solving adult-stamps-as-another-adult, which inside a household is nobody's problem. So: adult-verify is the honest size of the real threat, keeps the whole build in the frontend (reuse verify_profile_pin + the existing approval path, zero data-layer change), and under it decided_by = auth.uid() = the session owner is HONEST rather than a lie. The build fences to quest turn-in only; redemption/spend-commit has no wall surface to attach to (no pending-redemptions view exists — recon §5) and stays off the wall until deliberately built. approved_by is overridden to the session owner, NOT the cosmetic active-member tile (which would wrongly stamp a kid).
REPLACES: Tightens decision "adult/kid are permission tiers / PIN gates the ledger" (2026-07-17, NOTED) from bare "PIN-on-mint" to "PIN-on-mint, adult-verify, session-owner-attributed." Supersedes the same-session identity-bound framing.
STATUS: LOCKED

---

DECISION: Commit-decider attribution is not server-bound to a proven actor — a P4×L8 input, audited as a class. approve_redemption's decided_by is session-locked (auth.uid(), cannot record a different proven adult); quest-approval's approved_by is client-asserted with no server-side tie to a PIN/proof event. Correct on the phone only by accident of one-session-one-adult.
DATE: 2026-07-18
WHY: Surfaced by the wall-commit recon. The server gates every commit on has_role(auth.uid(),'parent') alone — no commit RPC requires or checks a PIN server-side; the PIN is a client-side re-proof layered on already-legitimate session authority. That's fine for gating the UI, but it means the DECIDER field is not provably tied to a specific proven adult: approved_by is trusted from the client (the wall would send the honest session id, matching phone behavior — not a regression, not a repair), and decided_by is frozen to the session owner. The wall didn't create this; it EXPOSED it, because the shared session + cosmetic tiles are where one-session-one-adult stops holding. Own-hold-inert; distribution-grade real. Audit alongside the other bespoke write surfaces (wall_request_redemption household-scope, the 2026-07-16 Data-API grant) per the "audit every SECURITY DEFINER / privileged writer as a class" rule. The fix, if the audit wants it, is actor+PIN RPCs that verify server-side and stamp the verified actor — the same data-layer build the adult-verify walk-back deferred.
REPLACES: Nothing — new banked P4×L8 input.
STATUS: NOTED

---

DECISION: The redemption → activity_log write path is incomplete and inconsistent. parent_self_redeem does not append to activity_log at all; the one redemption that did log was written by a later path and keyed object_id to the REWARD id, not the redemption id. The fix is ONE correct redemption-feed write, not a patch on one function.
DATE: 2026-07-18
WHY: Found during the SnowDad spendable cleanup — two of three self-redemptions had no feed row, and the third's activity_log row was mis-keyed. So a whole class of redemption (parent self-redeem) is invisible in the Hearth/activity feed, and even the logged case points at the wrong object. On a phone this is cosmetic; on the WALL the feed ticker is load-bearing ambient presence, so a redemption class silently missing from it is a real gap — and it parallels the membrane principle that every value-moving event should leave a feed trace. Direction: make the redemption→activity_log write a single correct path (append on approval, keyed to the redemption), rather than bolting append_activity onto parent_self_redeem in isolation. Sibling finding, same cleanup: member_spendable silently clamps overdraw to 0 (GREATEST(...,0)) — 150 spent against 106 was allowed and hidden; open question whether the redemption pre-check ran or parent_self_redeem skips the balance gate. That clamp/overdraw question is a P4×L8 input (the spend path may not enforce the balance).
REPLACES: Nothing — new finding from the 2026-07-18 spendable cleanup.
STATUS: NOTED
---

DECISION: Claude Code recon syncs to origin/main BEFORE reading. A local working clone is a disposable scratch checkout, never a source of truth.
DATE: 2026-07-17
WHY: For roughly a week, every Code recon silently read a STALE local clone and had to `git pull` mid-task to reach reality. The 2026-07-17 ticker recon opened by concluding "wall/display mode is never built" — false; the clone was 78 commits behind origin — and only self-corrected because the agent thought to check origin. A stale working copy fails SILENTLY: it does not error, it answers from an old tree and makes the recon confidently wrong. This is the same failure shape as raw.githubusercontent serving stale content over the GitHub API, and the same resolution: the authoritative source wins, the cache never does. For docs, the API beats the CDN. For code, origin/main beats the local clone. Code still keeps a working tree (it needs one to grep and trace imports across files); what's forbidden is TRUSTING a tree that hasn't been synced this session. Step zero of every recon: pull. Formalizes what was already de facto true — we work off live origin, not local — and which Scott had flagged across several prior chats before it was captured. The capture debt was itself the failure: a known operational truth, left "fine and okay" and unlogged, returned as a false shock a week later.
REPLACES: Nothing — new rule. Makes explicit an existing practice.
STATUS: LOCKED

---

DECISION: The wall / display mode is a PROPOSE-only surface. It never mints, spends, approves, or edits. On a shared surface, committer authority is re-proven per action (PIN), never granted to a cosmetic member tile.
DATE: 2026-07-17
WHY: The wall runs as ONE shared parent-authenticated session wearing cosmetic member tiles (consistent with kid-auth DECLINED 2026-07-10 and the walk-up trust boundary NOTED 2026-07-15 — the "active member" switch is convenience, not a security boundary). It follows that every action a member tile can take is a PROPOSE action: claiming a bounty and turning in a quest submit to the existing adult approval queue (no mint); redeeming a reward creates a PENDING request (no debit — verified: the existing flow debits only via approve_redemption, and member_spendable = sum(approved earnings) − sum(approved redemptions)); calendar events are VIEW-ONLY (EventDetail's Edit/Delete are suppressed on the wall, because canEdit() is always true under the shared parent session and destructive edits must not sit on an open kiosk). The membrane, applied to a wall: it SHOWS and PROPOSES; it does not touch household data or currency without the commit gate. See the companion PIN-gates-the-ledger decision for where the line falls precisely.
REPLACES: Nothing — new; formalizes the wall's trust model built 2026-07-17.
STATUS: LOCKED

---

DECISION: "Adult" and "kid" are PERMISSION TIERS, not family relationships — committer vs. proposer. On the wall, the PIN gates exactly one thing: embers minting or being spent. Everything else is open.
DATE: 2026-07-17
WHY: Worked out from a live observation — an adult's quest turn-in on the wall landed in AWAITING APPROVAL instead of auto-clearing as it does on the phone. The reframe: "adult" is the committer tier (trusted to approve/mint/spend/self-clear/enter display mode) and "kid" is the proposer tier (initiate only). This authority model ALREADY EXISTS in the codebase (approver vs. submitter); "adult/kid" is only the skin (see the parking-lot role-label-retirement note, which already named this). THE WALL-SPECIFIC CAVEAT is the load-bearing part: on an AUTHENTICATED surface (the phone) the tier travels with the session — the account IS provably that adult — so auto-approval is safe. On a SHARED surface (the wall) the tile only CLAIMS a tier; anyone in the room can tap the "SnowDad" tile, including a kid. So committer trust cannot be granted to a cosmetic tile — it must be RE-PROVEN per commit, and the only proof on the wall is the PIN. Scott's line, agreed: the PIN is required if and only if the action MINTS or SPENDS embers (the one irreversible, real-value event). Browsing, tapping tiles, opening the Vault, viewing a quest, turning in for the queue — all open, none move value. This tightens the earlier propose-vs-commit framing to a sharper test: does it touch the ember ledger? If yes → PIN. If no → open. Consequence: the wall's adult-turn-in-queues behavior is likely CORRECT (the membrane refusing committer trust to an unauthenticated tile), and the right build is PIN-on-mint (an authenticated adult commits directly from the wall), NOT auto-approve-because-the-tile-says-adult. Candidate for promotion to master-spec (the authority model is design truth, not build state) once verified.
REPLACES: Nothing — reframes existing "adult/kid" language as a permission model; tightens the 2026-07-17 propose/commit wall framing to "PIN gates the ledger."
STATUS: NOTED — wants a recon of how the existing approver/submitter model gates each action before it's LOCKED / promoted to master-spec.

---

DECISION: wall_request_redemption — a thin SECURITY DEFINER proxy that inserts a PENDING redemption on behalf of a picked member. No debit, no approval. Named P4×L8 audit input.
DATE: 2026-07-17
WHY: The existing redemption INSERT RLS requires requested_by = auth.uid(), which correctly blocks a kid-profile insert under the wall's shared parent session. Rather than weaken that policy, the wall routes redemption through wall_request_redemption — a SECURITY DEFINER RPC that inserts a pending row on behalf of a picked household member, with no debit and no approval. Same on-behalf shape as claim/turn-in, which needed no new path (the parent-role RLS exemption already permits a parent session to claim/submit for any member — confirmed by the 2026-07-17 recon). SECURITY DEFINER bypasses RLS by design, so the function body carries the entire safety burden. THE AUDIT LINE ITEM: confirm the RPC enforces household scope on the picked member_id — a caller must not be able to pass an out-of-household member. Inert on Scott's own hold; a distribution-grade concern. This is the SECOND bespoke SECURITY DEFINER write on the P4×L8 surface, alongside the 2026-07-16 Data-API grant finding — audit them as a class, per the 2026-07-15 "audit every SECURITY DEFINER writer as a class" rule.
REPLACES: Nothing — adds to the banked P4×L8 inputs.
STATUS: NOTED — feeds the P4×L8 audit.

---

DECISION: The wall Vault lives inside the member popup (behind the tile tap), affordable-only, audience-filtered. Data freshness is interval polling (~10s), decoupled from the feed ticker — never realtime.
DATE: 2026-07-17
WHY: Two settled wall calls, bundled because both were worked out and agreed this session.
     VAULT PLACEMENT: The Vault is person-scoped and it SPENDS — the opposite of the glanceable, household, stateless accordion zones (Bounties / Hearth / Ranks). Bolting it in as a fourth accordion zone was the wrong shape (it broke the idle-timer's three-zone resting state, and "Rewards" was off-vocabulary flat English — it is "the Vault" everywhere). Correct home: the member popup, reached by the tile tap that ESTABLISHES whose embers are in play, so no "who's redeeming?" picker is needed there (the tap already answered it — the same three-laws logic that put a picker on bounty-claim, where the household banner has no tile-tap). The popup shows the member's spendable balance (member_spendable) and a Vault button that swaps the popup body to that member's rewards. The wall Vault shows ONLY affordable rewards (member_spendable ≥ cost), mirroring the phone Vault's curated view — on a wall, "what can this member get right now" is the correct at-a-glance filter, not the full catalog. Audience-filtered via the existing reward.audience flag. Empty state (balance below cheapest reward) shows a warm, on-theme "go earn" nudge, not a blank panel. (Favorites deferred: per-member favorites can't be read from one shared login's localStorage — see per-profile-persistence in parking-lot; the empty-state message becomes a two-case branch once favorites exist.)
     DATA FRESHNESS: The wall stays current via a fixed ~10s poll (the PendingWaitingScreen ~8s pattern), NOT a Supabase realtime/websocket subscription — a wall needs ambient freshness, not sub-second push, and the websocket lifecycle is cost with no benefit here. ("Live" was loose shorthand; corrected to interval polling.) HARD RULE: the data poll and the feed ticker's scroll animation are INDEPENDENT clocks and must never gate each other. Coupling the poll to "after one feed rotation" was explicitly rejected — it makes staleness scale with feed length, so freshness gets WORSE when the household is busiest. (Ticker seamless-loop is a doubled strip animating to one copy's width — geometry, not remount; the poll swaps inner items in place and never remounts the animated node.)
REPLACES: The short-lived "Rewards" accordion zone (built and removed same session); any "live"/"realtime" wording for the wall.
STATUS: LOCKED

---

DECISION: There is no member "class" color system. Member color is memberColor() identity color.
DATE: 2026-07-17
WHY: Recon correction. "Color pills by class (Forge/Garden/Keep/Hall)" — repeated in the 2026-07-16 parking-lot wall-calendar note and in jAIne's framing — describes a system that does not exist. Forge/Garden/Keep/Hall live only in the avatar-art roster categories (avatar-review.tsx, itself flagged dead/leftover) and in starter-quest categories (starter-quests.ts); neither is a stored member trait, neither drives any color. What actually colors members is memberColor() in src/lib/calendar-colors.ts: name-override for the four core folk, else stored profiles.color, else a deterministic hash into an 8-hue jewel-tone palette. Same visual outcome intended, correct mechanism. Already wired end-to-end into the calendar (month-grid dots, day-list pills, EventDetail accent) and, as of 2026-07-17, the wall's event pills.
REPLACES: The "class color" framing in the 2026-07-16 wall-calendar parking-lot note.
STATUS: LOCKED
---

DECISION: The roster / switch-picker "no members" bug was a missing Data-API GRANT on the live DB — not a code defect. Live-schema drift is now a NAMED, REPEATING failure mode: when the repo is provably clean and the app still breaks, drift is the FIRST suspect, not the last.
DATE: 2026-07-16
WHY: The board Roster and the profile switch picker showed "No one in the household yet" for The W Drapers while Hold Ranks correctly showed all four members with real ember totals. Three theories died against the evidence, in order:
     (1) jAIne's NULL-legacy-rows hypothesis — a live read (via Lovable, read-only) confirmed all four profiles are status='active'. The 07-14 backfill worked.
     (2) The schema-drift-in-the-backfill theory — the 07-14 migration's `NOT NULL DEFAULT 'active'` plus an explicit belt-and-suspenders UPDATE is airtight as written; if it ran as filed, no pre-existing row could be anything but 'active'.
     (3) Hypothesis C (wrong session / wrong family) — Scott's screenshots showed the São Paulo campaign at 83%, the real join code (DQADRL), and real per-member ember totals. The session unquestionably resolved the W Draper family_id. If it were the wrong family, Ranks would have been empty too. It wasn't.
     Ranks-full + Roster-empty on the SAME session and SAME family narrows to the one thing that differs between those two surfaces: the query path.
     ROOT CAUSE (Lovable, live): the `public` tables carried ZERO Data-API grants for the `authenticated` role. Every signed-in query — roster, quest-assignment picker — was rejected with `permission denied for table profiles` BEFORE RLS ever evaluated. Ranks survived because it reads the `family_xp` VIEW, which predates the grant hole and carries its own access — which is why Ranks looking fine told us nothing about the grant state. Fix: restored SELECT/INSERT/UPDATE/DELETE to `authenticated` and ALL to `service_role` across all 14 tables; RLS still gates who sees which rows. Roster and picker now populate correctly, live, with all four members.
     WHY THIS IS THE HEADLINE: the defect lived in live DB state that NO migration file describes — the exact class master-spec already flags (`recurrence_day`, the `monthly` enum, both added out of band). This is the FOURTH instance. Both Code and jAIne were structurally doomed to miss it: both read the repo, the repo was clean, the truth was in the grant surface. The signature is now known and worth naming — code provably clean + still broken = suspect live-schema drift FIRST, before re-reading the same clean code a fifth time.
     THE RECORD, UNSENTIMENTAL: jAIne led hard on NULL legacy rows (wrong); called `b31c92c` a deploy landmine on that basis (wrong — active rows pass an active filter, so `b31c92c` was innocent all along; the correct reason to hold its deploy was only "don't ship what you can't explain," which held); and floated a NULL-path security asymmetry (wrong — see the P4×L8 note below). Three theories; the data killed two outright and the grant finding retired the third. TRUST-THE-LANE was correct here: Lovable had live-DB eyes neither Code nor jAIne had, and it found what neither could from the repo.
REPLACES: Nothing — new finding. Generalizes the live-schema-drift failure mode (2026-07-14: `recurrence_day`, the `monthly` enum) into a named first-suspect rule.
STATUS: LOCKED (finding + rule).

---

DECISION: The Data-API grant surface is a named P4×L8 audit line item. A grant hole that silently DENIED just surfaced; the audit must confirm there is no sibling hole that silently ALLOWS.
DATE: 2026-07-16
WHY: The roster fix restored grants broadly — SELECT/INSERT/UPDATE/DELETE to `authenticated`, ALL to `service_role`, across all 14 tables. RLS still constrains row visibility (load-bearing, confirmed by Lovable and consistent with the code), so this is not an alarm — the hole that existed FAILED CLOSED (denied everything). But "grants were missing entirely and got restored broadly" is exactly the kind of live DB-state change the audit should see the FINAL STATE of, not take on faith. Named line item for P4×L8: what Data-API grants does `authenticated` actually hold on each of the 14 tables, and does RLS fully constrain every one of them? A hole that denies is benign; a sibling hole that ALLOWS is the finding.
     CHECKED-NEGATIVE, same session: jAIne hypothesized a NULL-handling asymmetry — NULL status treated as "grant access" on one path (`current_family_id()`) while "exclude" on another (the roster filter). Code read both: `current_family_id()` returns NULL for any non-active caller, and every family-scoped RLS policy compares `family_id = current_family_id()`, which is NULL for any real family_id → fail-closed, access denied. The roster filter also excludes non-active. Both paths AGREE. No asymmetry, no escalation vector through that function — and even in the worst-case drift scenario (a row somehow NULL live), `current_family_id()` still fails closed. Logged as a checked, no-gap finding for the P4×L8 pile.
REPLACES: Nothing — adds to the banked P4×L8 inputs (the 07-15 live-catalog dump, the 25 Supabase linter warnings).
STATUS: NOTED — feeds the P4×L8 audit.

---

DECISION: "All quiet at the hold" is CORRECT behavior, not a bug. The line reports the ADULT'S ACTION QUEUE — "nothing needs your seal" — not household inactivity.
DATE: 2026-07-16
WHY: status.md carried "the empty-board eulogy is a lie on a live board" as an open bug — "ALL QUIET AT THE HOLD" rendering directly above "N open" and a fresh completion. It is not a lie. It is a TRIAGE line: it means zero items demand the adult right now — no pending admits, no approvals waiting, no alerts. Open bounties are posted work waiting for a KID to claim (state, not a demand on the adult); a logged completion needs no seal. On the boards where this was flagged, the adult's action queue was genuinely empty, so the line was true. It reports demand-on-YOU, not activity-in-the-house — and that is exactly the right thing for an adult's board to lead with.
     This does NOT touch the separate, still-live Pip first-run concern: a brand-NEW household landing on an empty board needs a DOORWAY ("post your first quest with +"), not silence. That's an onboarding-activation gap (LOCKED 2026-07-12, gated behind distribution), not this line being wrong. Two different problems that happened to share a screen; jAIne conflated them and kept re-flagging a resolved item off stale canon.
REPLACES: The "empty-board eulogy is a lie on a live board" open bug in status.md (⬜ → resolved, working-as-intended).
STATUS: LOCKED.

---

---

DECISION: The "active member" switch is a convenience layer, not a security boundary. On a shared
device signed into a parent's account, physical possession = parent authority. Accepted for the
walk-up model; flagged as a live input to the P4×L8 / distribution gate.
DATE: 2026-07-15
WHY: Recon on the profile-switch / PIN flow (the same pass that produced the b31c92c roster fix)
     confirmed client and server AGREE on parent identity — no escalation mismatch. set_profile_pin's
     has_role(parent) check and the client's viewerIsParent both read the real signed-in auth.uid();
     switching into a PIN-less adult profile hard-gates (setActiveMemberId fires only after the PIN
     RPC resolves); Cancel returns to the picker with no bypass. All SAFE.
     THE SEAM: neither check consults which profile is locally "active." On a shared device where the
     real login is a parent's own account (the standard case for the no-login walk-up model), a kid
     physically holding that already-unlocked device — while the UI shows "acting as kid" — can tap a
     PIN-less adult tile, get the set-PIN dialog, and successfully set that adult's PIN, because the
     server correctly sees a parent session. Same root enables the "Forgot PIN?" clear-and-reset. The
     "who's acting" switch provides no cryptographic isolation; it's cosmetic.
     WHY ACCEPTED: this is the walk-up kiosk model working AS DESIGNED — its trust boundary is
     physical possession of an unlocked device, not per-profile identity. Directly consistent with the
     2026-07-10 kid-auth DECLINED decision, which already reasoned that impersonation is a
     physical-access problem, not an auth problem. For a household where an unlocked parent phone in a
     kid's hands is already game-over, the downstream PIN dance is not the real boundary. Rejected:
     bolting a confirm()/re-auth onto this one flow tonight — that's piecemeal patching of a property
     the whole model shares, and the over-correcting failure mode. The distribution-grade version
     ("acting-as provides zero isolation on a less-trusted device") must be judged holistically by the
     tenant-isolation audit, not one dialog at a time.
REPLACES: Nothing — records a seam implied by the 2026-07-10 kid-auth decision.
STATUS: NOTED — revisit trigger: the P4×L8 audit, or any move toward untrusted-device distribution.

---

DECISION: The 07-14 batch was logged from INTENT, not landed code. Every frontend change a migration claims to have made is grep-verified against the actual tree before it's called shipped.
DATE: 2026-07-15
WHY: Three separate live defects today all traced to 07-14 work the decision log recorded as "shipped": the admit/deny enum bug, the reconcile-branch escalation, and the recurrence_day frontend removal that was written, STASHED, and never committed — crashing quest creation in prod for real users. "removed from both frontend forms" was in the log; the code disagreed. Design-complete was recorded as shipped. Generalizes the existing "a doc's record of a decision is a claim, not a verification" (2026-07-12, Feast→Hall): this is the same disease, three instances in one day. The fix is cheap — grep the tree.
STATUS: LOCKED

---

## 2026-07-15

```
DECISION: A SECURITY DEFINER function that grants or reconciles a user_roles row derives the role
from the trigger-protected profiles.role column — NEVER from a caller-supplied parameter.
DATE: 2026-07-15
WHY: The 07-14 admit-on-approval migration — reviewed before commit — shipped a LIVE privilege
     escalation. complete_household_setup's "already has a profile" reconcile branch inserted a
     user_roles row from the caller-supplied _role param, unchecked against the profile's real role.
     Because the whole authorization model routes through has_role(), which reads user_roles (not
     profiles.role), an active kid calling complete_household_setup(_role => 'parent', …) minted
     themselves a parent role everywhere — while profiles.role still read "kid." Nothing caught it:
     enforce_profile_role_change guards writes to profiles, this wrote user_roles; the "deny direct
     role inserts" policy doesn't apply because SECURITY DEFINER runs as owner. The 07-15 live-DB
     audit confirmed it was reachable — EXECUTE was held by anon, authenticated, service_role,
     sandbox_exec, postgres.
     WHY profiles.role is the safe source: enforce_profile_role_change blocks a non-parent from
     changing it, so that column only ever reflects what a real parent granted. A caller parameter
     carries no such protection. The invariant the whole schema rests on — "only a parent-checked
     function writes user_roles" — must not be broken by trusting caller input for the role value.
     Rejected: deleting the reconcile branch entirely — kept it for legitimate self-heal of a
     missing role row, but re-sourced it from profiles.role.
     FIX (Lovable, reviewed, verified by re-reading the branch): reconcile now
     `INSERT ... SELECT auth_user_id, p2.role FROM profiles p2 WHERE p2.id = auth_user_id`.
     Create and join branches and the signature unchanged.
     THE SCAR: this is the second wound of Finding #1 (kid self-declaring parent), which the 07-14
     data layer was supposed to have closed in the same pass — and it re-opened through a DIFFERENT
     door (the reconcile branch) than the one that was hardened (the join branch). Closing one path
     to escalation does not close the class. Audit every SECURITY DEFINER writer of user_roles as a
     class, not one function at a time.
REPLACES: Nothing — new rule. Adjacent to the (correctly closed) join-code bypass #1; this is a
     separate escalation the same feature carried.
STATUS: LOCKED
```

```
DECISION: Data-layer security fixes are verified against the LIVE database, not against the
migration's success report or a pre-commit review. The method: the DB owner extracts, an
independent agent judges.
DATE: 2026-07-15
WHY: The escalation above was shipped by a migration that ran successfully AND was reviewed before
     commit. Both are claims about intent; neither is verification of the deployed state. Two facts
     forced the method: (1) Lovable Cloud's Postgres is not reachable by local tools, so the live
     truth sits behind Lovable's wall — Lovable is the only agent already inside it; (2) Lovable
     wrote the fix, so asking Lovable to audit it is the author grading its own exam — the same
     "self-report is a claim" failure family already on the books (2026-07-11, 2026-07-12).
     THE SPLIT: Lovable runs read-only introspection (pg_proc, pg_policies, trigger defs, grant
     surface) and pastes the RAW catalog back — camera, not judge, explicitly told not to summarize
     or fix. A separate agent (jAIne, in-context) runs the audit against that text adversarially.
     Live truth AND an independent judge, for the price of one read. As a bonus it moots the
     stale-local-clone problem for the audit — production is the artifact, not the clone.
     Generalizes the standing law: migration files (and pre-commit reviews) are INTENT; the live
     database is TRUTH. The gap opens whenever a policy is hand-patched in the SQL editor without a
     migration — then the file lies with a straight face.
     Watch item, minor: the "raw dump = truth" pipe has a person/agent in it — the extraction agent
     left its own scratch reasoning in one dump. Harmless here (final SQL was consistent), but the
     camera can editorialize; read what comes back, don't assume it's inert.
REPLACES: Nothing — new operating rule for data-layer security verification. Complements the
     auto-accept and night-eligibility rules (2026-07-12).
STATUS: LOCKED
```


2026-07-14

DECISION: The hub open-bounty count matches the board — future-dated bounties are hidden everywhere, via one shared predicate.
DATE: 2026-07-14
WHY: The Briefing hub counted all non-approved open bounties regardless of due_date (showed 8);
     the board and kid profiles gate on due_date <= today (showed 4). The board is the honest
     surface: it answers "what needs doing / what can I claim now," and a done or future-dated
     recurring quest should NOT appear — a monthly completed yesterday must leave the board or the
     board stops reflecting the real state of the house, which is the whole reason it exists.
     Rejected "4 now · 4 upcoming" label: that's the calendar's job, and it's surface creep on a
     number that just needs to be true.
     IMPLEMENTATION: all three surfaces now route through isOpenBountyVisible(quest, role,
     today = todayIsoDate()) in src/lib/quest-helpers.ts — sole source of truth for status +
     assignment + audience + the due_date<=today gate. todayIsoDate() defines the day-boundary
     string once. This kills the prior duplication (Briefing hand-rolled its query; board
     hand-rolled todayStr twice) — same disease as the feedHeat() duplication, same cure.
REPLACES: Nothing — resolves the 8-vs-4 mismatch found 2026-07-14.
STATUS: LOCKED

DECISION: Recurrence anchors to the calendar, not to approval date. Daily = today, Weekly = Monday, Monthly = the 1st. The recurrence_day picker is removed.
DATE: 2026-07-14
WHY: Recurrence was RELATIVE — handle_quest_approval spawned the next instance at approved_date + 7
     (weekly) or + 1 month (monthly). Relative recurrence DRIFTS: a quest approved late walks its own
     due date forward, so "weekly" lands on random weekdays and the due date depends on when an adult
     happened to approve — reintroducing exactly the human-admin-dependent drift Emberhold exists to
     beat, living inside the recurrence engine. Fixed calendar anchors kill the drift and are trivially
     legible: "weekly = Monday" is a fact a kid holds without the app.
     Monthly = the 1st ONLY. The per-quest "day of month" picker (1-31 + "Last day") is REMOVED:
     anything bespoke is a CALENDAR event, not a recurring quest. That is the membrane — recurrence is
     a small set of habit anchors, not a scheduler. ("We have an entire calendar feature for anything
     else." — Scott.)
     Weekly anchor is global Monday for now; per-household configurability parked (out-habit, don't
     out-feature).
     IMPLEMENTATION (shipped 2026-07-14): trigger weekly next-due =
     date_trunc('week', COALESCE(due_date, CURRENT_DATE)) + interval '1 week' (Postgres week starts
     Monday); monthly = date_trunc('month', ...) + interval '1 month' (the 1st). recurrence_day removed
     from both trigger functions, both frontend forms, and the column dropped. Live re-anchor: 8 rows
     before / 8 after, 7 re-anchored, 0 dupes, 0 orphans. PAST-DUE monthlies left in place, not swept
     forward — an undone chore is work still owed and must stay visible on the board (and sweeping it
     forward would collide with the due_date<=today gate shipped the same day, hiding it entirely).
     Discontinuity on the recompute was ACCEPTED — live users are informed Alpha testers.
REPLACES: The relative +7 / +1-month recurrence spawning in handle_quest_approval (migration
     20260710130515), and the recurrence_day column and its picker.
STATUS: LOCKED

DECISION: Join-code hardening (Finding #1) is admit-on-approval, implemented lean on the profiles row. CLOSED at the data layer.
DATE: 2026-07-14
WHY: Locked 2026-07-13 as admit-on-approval (reuse the approval mechanism, don't build a parallel
     product). Recon 2026-07-14 corrected the load-bearing premise: the Command Center approval queue is
     HARDCODED to quests (ZonePending queries quests directly) and does NOT already handle admits — the
     7/13 note's "it already handles hold admits" was false, same failure family as Feast->Hall. The
     strategy survived; the "already exists" claim did not.
     Rejected HEAVY (a holds table + admission-requests table + generic queue refactor + new RPCs +
     new route + notifications): building an admissions subsystem to gate what is fundamentally
     second-device pairing for your own household is scope creep wearing a security badge.
     Chose LEAN: profiles IS the membership row. A join-by-code lands the profile status='pending';
     current_family_id() returns NULL for a non-active profile, so every family-scoped RLS policy
     denies by construction — fail-closed everywhere through ONE chokepoint. The self-selected role is
     stored as requested_role (advisory); no user_roles row is written at join. A parent admits AND
     confirms the role in one action (admit_pending_member(_profile_id, _confirmed_role)); the confirmed
     role is authoritative, closing the second wound (kid self-declaring parent) in the same pass.
     Enforced at the trigger layer (enforce_profile_role_change), not policy alone — mirrors the
     a_enforce_quest_update_authority guarantee for quest approval.
     LANE: data layer through Lovable (owns the DB, proven path); a CRITICAL fix is not the guinea pig
     for the still-unproven external-push migration. Frontend is Code's, next session.
     STILL OPEN (not closed by this): the admit UI does not exist, and the verification audit was
     written but never run. The hole is clamped and unproven, not stitched.
REPLACES: The 2026-07-13 admit-on-approval entry's premise that the existing queue already handles
     admits. Strategy stands; mechanism is lean-on-profiles, not reuse-the-quest-queue.
STATUS: LOCKED (design + data layer) / OPEN (frontend + verification audit)

DECISION: Frontend defaults to the SAFE role when admitting a pending member.
DATE: 2026-07-14
WHY: admit_pending_member requires the parent to pass a confirmed role, and the RPC ignores the
     joiner's requested_role in favor of it — the data layer is clean. But if the admit UI pre-selects
     "parent" just because that's what was requested, it reintroduces the rubber-stamp through the
     interface. The requested role is shown as CONTEXT ("wants to join as: Parent"); the toggle defaults
     to KID; promoting to parent is a deliberate tap. Bias toward the safe direction — the same principle
     as the avatar-crop inward bias, applied to permissions: when a process can be wrong, choose which
     way it's wrong.
REPLACES: Nothing — new decision, binds the (unbuilt) admit UI.
STATUS: LOCKED (applies when the admit UI is built)

```

# Session 2026-07-13. Add these ABOVE the current top entry.

---

DECISION: Join-code hardening (Finding #1) — admit-on-approval.
DATE: 2026-07-13
WHY: Possession of a join code currently grants full parent admin with no expiry,
     rotation, or gate. Chose admit-on-approval over expire or rotate because the
     join flow's real job is second-device pairing for your OWN household, not cold
     stranger recruitment — and a parent adding their own second device already has
     a phone out. Expire and rotate both depend on a parent remembering to do
     maintenance, which is the exact drift-and-rot failure mode Emberhold exists to
     beat. Admit-on-approval closes the hole completely: an unapproved join can't
     complete.
     IMPLEMENTATION: Do not build new. The Command Center quest-approval queue
     already implements "adult reviews a pending item and taps approve" (and already
     handles hold admits). A pending join request is another object in that existing
     queue. Reuse the mechanism; do not author a parallel one.
REPLACES: Nothing — new decision. Resolves the open call in parking-lot.
STATUS: LOCKED

---

DECISION: Finding #6 (kid self-approval / ember self-minting) — CLOSED.
DATE: 2026-07-13
WHY: Re-audit traced the full chain in theemberhold and found the path BLOCKED by an
     independent defense the original finding missed. The quests UPDATE policy is
     row-gated only (family + claim/assignment), so on the policy alone a kid could
     write status='approved'. BUT a second BEFORE-UPDATE trigger,
     a_enforce_quest_update_authority (migration 20260627044607, a_ prefix forces it
     to fire before the approval trigger), raises "Only parents can approve a quest"
     and aborts the update before handle_quest_approval() runs. Independently, ember
     minting routes through append_activity(), which nulls ember_delta server-side for
     non-parents. Two independent gates.
     LOAD-BEARING NOTE: the block lives in TRIGGER logic, not the policy layer.
     Functionally sound today. Do NOT drop or refactor a_enforce_quest_update_authority
     without replacing its guarantee — it is the lock on the ember economy. Now flagged
     in master-spec schema (commit d00154a).
REPLACES: Original Finding #6 (real-looking path, actually blocked). #6 CRITICAL → CLOSED.
STATUS: LOCKED

---

DECISION: Unpatched security findings are held privately, not published.
DATE: 2026-07-13
WHY: The public canonical repo carried a full seven-row vulnerability map — mechanism,
     severity, root cause — for a LIVE app with 13 real users and children's data, with
     zero fixes written. The repo's own claim that findings 1-5 and 7 "disclose nothing
     an attacker couldn't already probe" was wrong: probing costs effort and blind
     guesses; a published finding removes both. This is why responsible disclosure has
     an embargo. Rejected: making the repo private (breaks the raw-URL fetch protocol,
     needs a PAT in a chat window). Rejected: rewriting git history (force-push, breaks
     clones, loud, and a motivated attacker reading git log -p was always going to find
     these). The scrub kills the drive-by, not the determined. The PATCH is the fix;
     this just stops advertising while the patch is written. Design intent stays public;
     the confession that intent isn't enforced does not. decisions.md entries hollowed,
     not deleted — append-only holds. Republish findings once patched.
     TRIGGERED BY: a cold sales-spam email (paas.build, "add payments to Lovable") that
     was itself nothing — but prompted the repo review that found the live vuln map.
REPLACES: The "safe to publish" note formerly in status.md.
STATUS: LOCKED

```
## 2026-07-12 (eve)

```
DECISION: Claude Code's lane is TEXT. Any task whose success criterion is VISUAL is outside it —
and briefing it harder does not help.
DATE: 2026-07-12
WHY: Four escalating attempts to crop the avatar roster programmatically, four failures — each one
     more sophisticated and none of them closer.
     (1) Fixed 236px radius: 0.30 confidence on all 48, silent fallback to a heuristic on every file,
         two spot-checks, "success."
     (2) Ray-cast to the rim: r=74-126 on a 512 frame. It locked onto INTERIOR bright features —
         lava cracks, glowing eyes — not the rim ring.
     (3) "Outermost non-background pixel": r=219-247. THE CONTAMINATION IS NON-BACKGROUND. It grew a
         circle around the fringe it was hired to delete and reported 0.97 confidence.
     (4) Re-slice from source sheets: found 64 circles across 19 sheets. There are 57. Five on one sheet.
     The root cause was never the algorithm. The success condition was "does this look right," and
     NEITHER AGENT IN THE LOOP CAN SEE. Code cannot see its output; jAIne cannot see it either, so every
     brief she wrote was inference dressed as instruction — and she grew MORE confident as she grew more
     wrong. Two blind agents converging is not a review process; it is a louder mistake.
     This EXTENDS "jAIne does not slice images" (2026-07-11) rather than repeating it. That rule named
     jAIne's sensory limit and routed the work to Code as the tool that "can look." Code cannot look
     either. It can READ. Those are different senses.
     THE LINE: Code owns anything it can verify by reading — code, config, strings, files, structure.
     The moment correctness is judged by eye, it belongs to Scott.
     Cost of learning it: one evening and zero assets. Nothing was overwritten. The source sheets being
     banked is the only reason that sentence is true.
REPLACES: Nothing — extends the 2026-07-11 lane boundary to cover Claude Code, which the earlier
     decision explicitly (and wrongly) named as the fix.
STATUS: LOCKED
```

```
DECISION: The avatar roster is cropped BY HAND, in GIMP, by Scott. Automation is DECLINED.
DATE: 2026-07-12
WHY: See above. Method: ellipse-select, fixed 1:1 aspect, snapped to the gold rim and BIASED 2-3px
     INWARD. Crop to selection, invert, fill #1A110B, scale to 512x512, export. No alpha.
     THE BIAS RULE IS THE POINT AND IT GENERALIZES: when a process is going to be slightly wrong,
     choose WHICH WAY it is wrong. Losing a sliver of rim is invisible at avatar size. Keeping one
     pixel of a neighbour is not. Stop asking a detector to be exact; ask it to be safe.
     Rejected: keep iterating on the script — four rounds of evidence say the tool is wrong for the job,
     and every round cost more than the manual cut would have.
     Corollary: casting happens DURING the cut, because that is the only moment Scott is looking at all
     of them at once. ~57 portraits into 48 slots. The surplus is Guildhall inventory that already exists.
REPLACES: The implicit assumption that avatar transport was a scriptable file-moving problem.
STATUS: DECLINED (automation) / LOCKED (manual method)
```

```
DECISION: Avatar transport is FRONTEND-ONLY — except for one migration, which is the cheapest possible
test of the biggest open question on the board.
DATE: 2026-07-12
WHY: Read-only recon of the app repo, verified directly rather than inferred:
     - Avatars are STATIC FILES bundled by Vite (src/assets/avatars/avatar-01..21.png, id 11 retired),
       imported as ES modules. Grepped storage.from( / getPublicUrl / bucket across src/ and supabase/:
       ZERO matches. There is no storage bucket. There never was one.
     - The picker reads a HARDCODED ARRAY — HERO_AVATARS in Avatar.tsx, one hand-written entry per import.
     - The choice persists as profiles.avatar_emoji (text, default '🙂'), overloaded to hold EITHER a
       literal emoji OR the string "hero:"+id. No CHECK constraint, no FK. Swapping which ids exist needs
       no schema change.
     - Gating of any kind is CONFIRMED ABSENT (grepped premium|locked|tier|entitlement|guildhall — zero
       hits in any avatar file).
     THE ONE MIGRATION: thirteen accounts already hold hero: picks. Reusing ids 01-12 with new art would
     SILENTLY turn every existing member into a different character — Scott's kids included. Precedent
     exists: migration 20260628220717 force-reset hero:11 to the default when that avatar was retired.
     The same one-line UPDATE is required here.
     This is not damage control. It is a RE-FORGE MOMENT — thirteen drifted users get a reason to open the
     app: "The hold has been re-forged. Choose your hearthmate."
     And it makes avatar transport the ideal first test of whether Lovable EXECUTES an externally pushed
     migration — the unproven question the entire build model hinges on. Low stakes, precedented, and
     reversible.
REPLACES: The status doc's characterization of transport as "Supabase storage upload -> picker slotting."
     There is no storage upload. That step does not exist.
STATUS: LOCKED
```

```
DECISION: Model routing by job type. The meter is usage, not credits.
DATE: 2026-07-12
WHY: A mechanical grep does not need a frontier model, and the tenant-isolation audit must not run on a
     cheap one.
       HAIKU  — recon, greps, file traces, mechanical edits, file drops. Finding and moving things.
       SONNET — build jobs with real latitude, authored off a loose brief. Needs taste; worth it.
       OPUS   — the P4xL8 tenant-isolation audit. An adversary is going to try to reach children's data.
                This is the one place on the board where cost-optimizing is the wrong instinct. Spend it.
     Observed cost of getting this wrong: a frontier model performed an extremely competent search of a
     folder full of Unreal Engine assets.
REPLACES: Nothing — new operating rule.
STATUS: LOCKED
```

```
DECISION: The 5-hour window is a SCHEDULING constraint, not a budget hierarchy. The resolution is
temporal separation, never reduced thinking.
DATE: 2026-07-12
WHY: Recorded because jAIne got this wrong out loud and Scott corrected it.
     The observation was real: a heavy Code job and a working session in the same window contend. jAIne
     inflated that into "design sessions are a cost center competing with the build," which is false and
     was corrected immediately: Code jobs run at night BECAUSE SCOTT IS ASLEEP. Design sessions run when
     he is awake and thinking. The windows do not overlap unless someone makes them overlap.
     THE THINKING WAS NEVER THE EXPENSIVE PART. THE TYPING WAS — and that is precisely what Code exists to
     absorb. Code exists so Scott stops paying Lovable to type. It does not exist so he stops paying to
     think.
     The record is unsentimental about this: every real unlock on this project came out of a conversation,
     not a commit. The Convergence Law. The membrane. Household-level unlocks only. Killing the admin
     surface. "Rendered strings are copy, not identifiers" — which caught a live vocabulary violation
     Lovable had sworn did not exist. None of those came from an agent executing a ticket.
     THE RULE: never schedule a heavy Code job into a design session's window. Never solve a contention
     problem by thinking less.
REPLACES: Nothing — new operating rule. Corrects a claim jAIne made and retracted in the same session.
STATUS: LOCKED
```

```
DECISION: Auth email branding is PROMOTED to the critical path. It is a Gate B/D blocker, not polish.
DATE: 2026-07-12
WHY: Recorded with its reversal intact, because the reversal is the lesson.
     jAIne initially advised DEFERRING it, reasoning that the only audience was a four-person test
     household who already knew what the app was. Scott accepted. BOTH OF US WERE WORKING FROM A
     HEADCOUNT THAT WAS WRONG BY NINE.
     Thirteen accounts exist. Scott's guild and confidants have been in the app for two weeks. They have
     all received an email from a sender called "Family-Quest-Board" — a Lovable scaffolding placeholder,
     a name that exists NOWHERE in the product — and the password-recovery mail landed in Junk.
     An auth email in a spam folder is not cosmetic. IT IS A FUNCTIONAL BREAK IN ONBOARDING: the person
     never arrives. And it is the first artifact a stranger ever receives from Emberhold — it PRECEDES
     the app itself.
     Mechanics, established: Porkbun charges NOTHING for DNS records; DNS management comes with the domain.
     The fix is an SMTP provider (Resend free tier: 3,000/mo, 100/day, one domain — orders of magnitude
     above need) plus domain-auth records. Cost: zero. The tollbooth we braced for does not exist.
     CAUTION, carried forward: any NS-record request must be inspected before pasting. Nameserver
     delegation is not the same as adding a TXT record, and it can take the domain off the air.
REPLACES: The 2026-07-12 (eve) NOTED decision deferring this to the pre-distribution bundle. RETRACTED —
     it was reasoned from a false premise about who was using the product.
STATUS: LOCKED
```

```
DECISION: A LOCKED decision with no landing check is a wish. Decisions must be verified against the
codebase, not against the doc that records them.
DATE: 2026-07-12
WHY: "Feast -> Hall" was LOCKED on 2026-07-03 — a rename AND a redefinition, described in the decision
     itself as a "cheap find/replace: label, enum value, animation-theme name."
     TEN DAYS LATER THE APP STILL SHIPS `Feast`. AvatarPicker.tsx filters on Forge/Garden/Keep/Feast.
     It was found BY ACCIDENT, during a read-only recon for an unrelated question. Nothing was looking
     for it. The 7/11 vocabulary audit ran a grep sweep and did not catch it, because the audit checked
     STRINGS against the law and nobody checked whether a DECISION had ever reached the product.
     This is the inverse of the 7/11 finding. That one said: an agent's self-report of compliance is a
     claim, not a verification. This one says: A DOC'S RECORD OF A DECISION IS ALSO A CLAIM. The decision
     log proves what was decided. It proves nothing about what shipped.
     ACTION: grep every LOCKED decision against the codebase. Assume Feast is not alone. This is cheap —
     it is a Haiku job.
REPLACES: Nothing — new QA principle. Generalizes "verify artifacts, not self-reports" from agents to
     documents.
STATUS: LOCKED
```

---

## 2026-07-12

```
DECISION: Claude Code can perform frontend build work via the two-way GitHub sync. The Lovable credit
ceiling is no longer the binding constraint on frontend throughput.
DATE: 2026-07-12
WHY: Trial job (the feed vocabulary bundle) executed end-to-end by Claude Code — grep audit,
     shared-helper refactor, tsc/eslint/build clean, committed c9069a6, pushed. Lovable pulled it and
     the change renders live. Lovable's own subsequent audit reads Code's post-fix files as canonical,
     confirming the pull. Code also outperformed the brief: it found Briefing.tsx already had a correct
     private feedHeat(), promoted it to a shared helper rather than duplicating the fix, and left a
     scoped, justified follow-up (vault.tsx TIER_LABEL duplication) instead of silently absorbing it.
     UNTESTED — DO NOT ASSUME: whether Lovable RUNS a migration file pushed from outside. Backend and
     schema work through Code remains unproven. Frontend only.
REPLACES: The "batch interlocking fixes ruthlessly / roughly one meaningful prompt on free-credit days"
     throughput rule, for frontend work.
STATUS: LOCKED
```

```
DECISION: The batching reflex is retired. Small, single-concern, well-scoped jobs to Code — one at a time.
DATE: 2026-07-12
WHY: Batching existed because Lovable credits made each prompt precious. Under Code the binding
     constraint is job weight and context, not prompt count. A fat multi-concern job burns more context
     and produces a diff that is harder to review; a bug in one item contaminates the signal on the
     others. One commit per item, each independently reviewable.
REPLACES: The batching rule in the session protocol, for Code jobs. Lovable prompts may still bundle.
STATUS: LOCKED
```

```
DECISION: Auto-accept is scoped by job type, not left on by default.
DATE: 2026-07-12
WHY: ON for bounded, reversible, single-concern implementation jobs. OFF for audits, security work, and
     anything that could touch supabase/. Auto-accept approves CHANGES; an audit is defined by producing
     none — so the setting can only ever fire when the agent has departed from the brief, which is the
     exact moment a human should be looking. The realistic failure is not misbehavior but helpfulness: an
     agent finding an obvious, safe fix mid-audit and applying it, collapsing the deliberate audit/fix
     seam that exists so Scott sees the finding before the patch. Compounded by the open unknown of
     whether Lovable executes externally pushed migrations — a .sql file is not "dangerous" by any
     heuristic Code has, and auto-accept would let one through.
REPLACES: Nothing — new operating rule.
STATUS: LOCKED
```

```
DECISION: Overnight / unattended Claude Code runs are gated on FAILURE MODE, not on trust.
DATE: 2026-07-12
WHY: Competence is established; the question is supervision cost. A job is night-eligible if it is
     bounded, reversible, verifiable by inspection, and produces no decision Scott must make mid-flight.
     Audits and security work are disqualified — their output is a judgment call, so running them
     unattended yields a document read the next morning anyway: zero throughput gain, at the cost of an
     unsupervised read of the security model. Anything touching supabase/ is disqualified until it is
     known whether Lovable executes externally pushed migrations.
     Corollary: Chrome QA (Layer 2) must not run against a site Code is actively changing. Code builds →
     Lovable pulls → Scott eyeballs → Chrome tests what landed.
REPLACES: Nothing — new operating rule. Complements the auto-accept scoping rule.
STATUS: LOCKED
```

```
DECISION: Vocabulary audits must grep case-insensitively and against the SOURCE value, not the rendered
string.
DATE: 2026-07-12
WHY: `ROUTINE` / `MILESTONE` had ZERO exact-case hits repo-wide. The violation was the
     `activity_significance` enum value `routine`, rendered raw and uppercased by CSS at paint. A grep for
     the offending screen text returns clean while the violation is live on the screen. This is the
     inverse of the existing law: rendered strings are copy, not identifiers — AND an identifier rendered
     raw BECOMES copy, with the transform between them invisible to grep.
REPLACES: Nothing — extends the vocabulary-law audit method.
STATUS: LOCKED
```

```
DECISION: The `rewards` INSERT policy is scoped to household OWNER, not to the ADULT role. Non-owner
adults cannot author rewards. Folded into Workstream 1 as a confirmed finding.
DATE: 2026-07-12
WHY: [REDACTED - mechanism held privately until patched. Restored post-fix.]
REPLACES: Nothing — new finding.
STATUS: LOCKED
```

```
DECISION: Email confirmation is REQUIRED. Signup runs through a holding state.
DATE: 2026-07-12
WHY: Auto-confirm was on since inception — every account in the product was created without any proof of
     control over the email address, and the app had NO code path for a user who exists but is
     unconfirmed. Turning confirmation on broke signup instantly ("could not start session"), because
     signUp() returns { user, session: null } and the app assumed a live session. Fixed forward rather
     than rolled back: a holding view ("ALMOST LIT — check your email"), a resend with cooldown, spam
     guidance, an escape hatch, plus handling for duplicate-email signup and unconfirmed sign-in.
     Auth email deliverability and sender identity remain broken — see status.md.
REPLACES: The implicit auto-confirm posture.
STATUS: LOCKED
```

```
DECISION: Pip's first-run makes the loop KNOWN, not FELT. Comprehension only — no manufactured magic
moment. Delivered as additional onboarding SCREENS, not a live-site overlay tour.
DATE: 2026-07-12
WHY: Cold-start testing (P1×L1 / L6) showed onboarding ENDS at roster creation: the user lands on a board
     that says "all quiet" three different ways and never says what to do next. Setup was conflated with
     activation — they have members but have never created a quest, minted an ember, or redeemed anything.
     The fix rides the pattern that already exists (the Pip-narrated guided form), so it costs no new
     machinery; an overlay/coach-mark system would be a permanent new subsystem to own.
     Screens: (2) post your first quest · (3) stock the Vault with one reachable reward · (4) here's where
     everything else lives. All skippable. Net effect: the board they land on already has their own quest
     on it.
     Crucially, the tour does NOT stage the activation moment. The magic — a kid completes a quest, an
     adult approves, embers mint, embers buy something real — is powerful precisely because it is earned
     in practice. Staged inside a tutorial it becomes a demo, and a demo reads as a demo. The tour's only
     job is that the user knows what the loop is and how to reach it. Then get out of the way.
     Lists / Calendar / Campaigns are SHOWN, never toured — touring utilities inside the first-run flow is
     a membrane violation.
     Because every screen is skippable, the empty board must ALSO be built as a doorway, not a eulogy:
     "No quests yet — post your first with +."
REPLACES: The parking-lot "Pip onboarding flow" item, which assumed a tour.
STATUS: LOCKED
```

```
DECISION: Quality (the 5-star at approval) is a SIGNAL, not a modifier. It never affects embers.
DATE: 2026-07-12
WHY: The real use case is "approved, but half-assed" — a parent wants the effort noted without
     withholding the reward. Metering embers by quality turns every approval into a negotiation at the
     point of handoff, which is precisely the household conflict Emberhold exists to remove, and it
     complicates the economy for no gain. Quality instead carries the parent's editorial voice: the embers
     are unconditional, the note is not.
     Open before it ships: (1) is quality visible to the KID, or adult-only? A permanent public grade on a
     kid's board is a different and worse product. (2) What CONSUMES it? Almost certainly the weekly recap
     ("6 quests, two rushed") — aggregate, low-drama, at a moment of reflection rather than at handoff.
     Quality therefore binds to the recap and rides its timeline.
     Today it is collected, displayed on quest detail, and consumed by nothing. Until a consumer exists it
     is a concept, not a feature, and must not be presented as finished.
STATUS: LOCKED (direction) — consumer and kid-visibility still open.
```


---

## 2026-07-11

```
DECISION: The Lovable codebase is now GitHub-synced (SnowBroScott/theemberhold, private, two-way). Whether
          Claude Code can therefore do BUILD work — bypassing the Lovable credit meter — is an OPEN QUESTION
          to be tested, not an assumption to be acted on.
DATE: 2026-07-11
WHY: Lovable's GitHub sync is two-way: Code writes to the repo, pushes, Lovable pulls it in. If build work
     lands cleanly that way, it bills against the Claude subscription instead of Lovable credits — which
     demolishes the constraint behind "batch interlocking fixes ruthlessly; the floor on a free-credit day is
     roughly one meaningful prompt." That rule has shaped every session to date.
     But three things are UNKNOWN and must not be assumed:
     (1) Do migrations actually APPLY? supabase/migrations/ is in the repo, but it is unconfirmed whether
         Lovable runs a migration file pushed from outside or whether the file simply sits there while the
         database stays untouched. This is the whole ballgame — frontend-only vs. genuinely full-stack.
     (2) Two-way sync means two-way CONFLICTS. Two agents can now write the same codebase.
     (3) Lovable is native to its own 576 commits of scaffolding. Code is not, yet.
     THE TEST: give Code the already-written BOUNTY POSTED / ROUTINE-MILESTONE string bundle. Pure frontend,
     no migration, no schema, tiny blast radius. Run it BEFORE Workstream 1 — it's cheap, and if it passes it
     changes how everything after it gets built.
     Also worth recording plainly: this sync is the FIRST BACKUP the app codebase has ever had. Until today it
     existed only inside Lovable.
REPLACES: Nothing — new. If the test passes it will supersede the credit-ceiling reasoning behind the
          "batch ruthlessly" working rule.
STATUS: NOTED
```

```
DECISION: The 2026-07-03 avatar roster is ABANDONED and fully regenerated. 48 characters, 12 per class,
          3 per generated sheet.
DATE: 2026-07-11
WHY: The four "roster boards" were never master assets — they were CONTACT-SHEET PREVIEWS. Investigation found
     ~170px per character (the engine's ~1254px canvas divided by 32), a bottom band the engine had visibly
     squashed when it ran out of canvas, a bottom row clipped off the edge of the image, and circles packed so
     tightly they overlapped their neighbours. A picker thumbnail at 72pt on a 3x phone needs ~216px. There was
     no higher-resolution original — the sheet WAS the master.
     THIS IS WHY AVATAR TRANSPORT SURVIVED EIGHT DAYS OF DEFERRAL. It was never a file-moving problem. The
     asset did not exist. Three separate attempts to "just crop them" failed because the thing being cropped
     was not shippable, and the failure kept presenting as procrastination.
     Rejected: upscaling the existing roster — cannot invent detail that was never rendered, and does not fix
     the squash or the clipping.
     Rejected: re-rendering individual characters at higher resolution from the sheet — unproven fidelity; the
     engine returns a DIFFERENT dwarf.
     Accepted cost: a new cast. Generation is free on the work account; roster slots are not. Generate wide,
     ship narrow — the surplus becomes a Guildhall cosmetic drop that already exists.
REPLACES: "Avatar roster expands to 10 per class (40 base) + 4 cool-register = 44" (2026-07-03), and its four
          contact sheets. Free/paid split TBD is inherited and still open.
STATUS: LOCKED
```

```
DECISION: The avatar-generation playbook's central rule — "generate the FULL roster in ONE run" — is REVERSED.
          Three characters per sheet.
DATE: 2026-07-11
WHY: The reasoning behind the old rule was sound and the outcome was still unusable, which makes it the most
     instructive failure in the project. A cross-roster anti-repeat constraint genuinely can only bind when the
     engine sees the whole cast at once — but it bought that variety by spending the ENTIRE PIXEL BUDGET. The
     engine's canvas is fixed (~1254px) no matter how many characters you ask it to put on it.
     RESOLUTION IS THE CONSTRAINT, and everything else is downstream of it. 32 characters on one canvas is 32
     previews. Three per sheet yields ~600px each, circles that don't touch, generous dead air for the crop,
     and a geometry a script can slice blind.
     The anti-repeat benefit is recoverable — through the reference-sheet technique plus an explicit ban list
     carried forward between runs. The pixels are not recoverable at all.
     Validated across 19 consecutive sheets with zero composition drift.
REPLACES: Rule 2 of playbooks/avatar-generation.md, and point (2) of "The avatar generation method" (2026-07-03).
          The other four points of that decision survive; the playbook is rewritten around them.
STATUS: LOCKED
```

```
DECISION: THE CONVERGENCE LAW — a generator collapses onto every axis you do not pin.
DATE: 2026-07-11
WHY: Observed three times in one day, on three different axes, in three different classes.
     FORGE, unpinned on EMBLEM → issued nearly every character the same gold anvil-crest, lifted off the
       reference sheet and worn as a uniform.
     KEEP, unpinned on COSTUME → gave all three core characters the same silver circlet, blue cloak and
       crystal brooch. Three people, one wardrobe.
     HALL, unpinned on SCENE → put every character behind a tavern counter with a chalkboard sign and their
       goods laid out. Same composition, re-dressed.
     The defaults are not random. They are UNIFORM, and they are the same every time. The correction is always
     the same shape: name the axis, then give it options.
     Corollary: the reference sheet holds STYLE. It does not hold VARIETY. It cannot stop convergence on an
     axis that was never named.
REPLACES: Nothing — generalizes and supersedes the framing of "axis errors, not length errors" (2026-07-03).
          That finding was correct but under-scoped: it named two axes, and there are as many axes as there
          are things you forgot to say.
STATUS: LOCKED
```

```
DECISION: Character generation requires JOBS, and pose requires a MENU. Adjectives do not work.
DATE: 2026-07-11
WHY: Two independent instances of one underlying truth — a word in a prompt does not beat a default.
     JOBS: Keep's core register returned three interchangeable noble humans in blue robes. Re-run with trades
     named — stonemason, librarian, gate-guard — it returned a rune-carver with stone dust on her face, a
     librarian squinting through spectacles at something he clearly finds disappointing, and a snow-covered
     guard who has been outside a very long time. Same style block, same class, same lighting. The only change
     was that the characters had something to DO.
     POSE: "vary pose and expression boldly" produced the same three-quarter turn, chin up, eyes to camera, on
     every character in the roster — including a corgi. Replacing the adjective with an explicit list of head
     angles plus a "no two alike" constraint immediately produced a full profile, a straight-on, and an elder
     looking up and LAUGHING — the best character in the roster.
     Tell it THAT a character has a job; never WHICH job. Pin the axis, free the content. This is the lane rule
     applied to prompting, and it resolves the apparent tension with "loose briefs win."
REPLACES: Nothing — new rules in playbooks/avatar-generation.md.
STATUS: LOCKED
```

```
DECISION: Child-safety fences must be STATED in every generation run. The engine will not infer them.
DATE: 2026-07-11
WHY: Unprompted, the generator produced a warrior-woman in a chainmail bikini, and a wide-eyed baby amphibian
     raising a foaming tankard of ale. A "fantasy tavern" is not a defense — this is a chore app an eight-year-
     old opens to claim quests, and the read is instant.
     Standing clauses, every run: all characters fully and modestly clothed or armored; never any alcohol.
     Note the cost of the fix was ZERO. The tankard became a steaming teapot and the character was otherwise
     identical. The prop was never doing the work. The face was.
REPLACES: Nothing — new rule.
STATUS: LOCKED
```

```
DECISION: "Bounty" is banned from card-level UI. In the Open Bounties strip the redundant OPEN BOUNTY chip is
          removed entirely; on the main board, unclaimed quests carry an UNCLAIMED chip.
DATE: 2026-07-11
WHY: The vocabulary law holds "Quest" as universal object language and "Bounty" as a proper noun for the
     Briefing strip's feature name only. A per-card OPEN BOUNTY chip is bounty-as-object-language — the exact
     contamination the law exists to prevent. Inside the Open Bounties section the chip is also redundant with
     its own header. On the main board a chip DOES do work, but it must describe the quest's STATE, not rename
     the object.
     Rejected: relabeling to "OPEN QUEST" inside the strip — still redundant with the header.
     Rejected: restyling the chip — styling does not fix a naming violation.
REPLACES: Nothing — this ENFORCES "Quest is the universal object term" (2026-07-04). That decision closed with
          "string audit across all UI surfaces still OWED." The audit is now done. The debt is paid.
STATUS: LOCKED
```

```
DECISION: The activity feed renders the canonical ember tier names (Dim / Warm / Hot / Blazing). The parallel
          ROUTINE / MILESTONE vocabulary is eliminated.
DATE: 2026-07-11
WHY: Heat is ONE dial. Brightness = heat = importance, across every surface. The feed derives its heat from the
     same ember-value tier ramp the cards do, so it must use the same names. A feed row reading MILESTONE while
     the quest card reads BLAZING is two dials for one concept.
     This was DRIFT, not design — nobody decided it, it simply appeared, and it would have hardened into canon
     if it had lived in a screenshot much longer.
     "Legendary" remains a campaign-completion override state, not a value tier — consistent with 2026-07-04.
REPLACES: The undocumented ROUTINE / MILESTONE feed labels.
STATUS: LOCKED
```

```
DECISION: Rendered strings derived from database identifiers are COPY, not identifiers. Audit them as such.
DATE: 2026-07-11
WHY: The `bounty_posted` enum value is an identifier and can live forever — nobody reads the database. But the
     feed row it renders ("New bounty: …", "BOUNTY POSTED") is a sentence a child reads, and it is squarely in
     scope for the vocabulary law. An audit that files these under "internal identifiers, not user-facing"
     misses them by CATEGORY ERROR. The test is not "is it derived from code." The test is "does it appear on
     screen."
     Corollary, and the more important half: an agent's self-report that strings are "already consistent" is a
     CLAIM, not a verification. Lovable reported the quest/bounty strings clean and made no edits; a screenshot
     an hour later showed OPEN BOUNTY stamped on six cards. Require the grep — file by file, with a
     justification for every survivor. That is what turns an item from asserted to closed.
REPLACES: Nothing — new standard. Applies beyond vocabulary: it is a QA principle.
STATUS: LOCKED
```

```
DECISION: The install-prompt description is: "Chores become quests. Quests earn embers. Embers buy real
          rewards. Your household, but fun."
DATE: 2026-07-11
WHY: This string is the door, and the door should be in English. Three escalating clauses mean a stranger who
     reads nothing else understands the entire product — each clause answers the question the last one raised
     (quests? so what. embers? so what. oh — REAL rewards). Internal vocabulary ("the Hold") is a reward for
     being inside, not a hook for getting in.
     Rejected: "gamified" — a tech buzzword; promises rather than describes.
     Rejected: "epic" — the same disease wearing a cape, and it oversells a product whose actual job is getting
     a nine-year-old to empty the dishwasher.
     "but fun" quietly admits household admin normally isn't, which is the shared joke with every parent who
     reads it. Self-aware beats aspirational at the door.
REPLACES: "turn household tasks into XP" — the last surviving user-visible XP string in the product, and,
          being in manifest.webmanifest, the FIRST thing a stranger read.
STATUS: LOCKED
```

```
DECISION: jAIne does not slice images. Tasks whose success condition is visual belong to Claude Code.
DATE: 2026-07-11
WHY: jAIne cannot see the images her own tools produce. A programmatic slice of the roster was attempted, the
     grid was wrong, and the output — two half-avatars per tile, cut through the middle — was delivered anyway,
     because there was no way to check it. The only reason the error surfaced is that Scott opened the file.
     This is a SENSORY limit, not a care limit, and no amount of diligence fixes it. Any task where the success
     condition is "does this look right" must go to a tool that can look. Claude Code reads the filesystem and
     can inspect its own output.
     Corollary: this gave Claude Code its first real job, and it is a far better first outing than a security
     fix — the failure mode is visible and harmless.
REPLACES: Nothing — new lane boundary.
STATUS: LOCKED
```

```
DECISION: Engine bundle — daily respawn VERIFIED. Promoted from DRAFT to LOCKED.
DATE: 2026-07-11
WHY: A completed daily reappeared fresh and correctly dated the following morning. This was the one unverified
     half of the 2026-07-10 engine bundle. The quest lifecycle is now verified end to end, not asserted.
REPLACES: The DRAFT status on "Engine bundle shipped" (2026-07-10), which was held pending overnight
          verification of the due_date rollover across midnight.
STATUS: LOCKED
```

## 2026-07-10

```
DECISION: Docs migrate to a version-controlled repo with undated filenames
DATE: 2026-07-10
WHY: The dated-filename pattern (master-spec-2026-06-29-eve.md) was hand-rolled version control, and it failed —
     two docs were lost, one unrecoverable, and a session was spent on archaeology. Git owns history for free.
     Also collapses the build-state / burndown-tracker split: two docs answering one question means neither
     gets updated. Doc-edit friction was the named trigger signal for this upgrade; it fired.
REPLACES: The Project-knowledge doc-set and the dated-file convention.
STATUS: LOCKED
```

```
DECISION: #8b (admin/reporting surface) parked behind the beta
DATE: 2026-07-10
WHY: Six unrelated things (redemption history, reward performance, weekly recap, Adventure Log, Quest-Log-as-report,
     ops glance) sharing a room because none had anywhere else to go — a dumping ground with a disclaimer.
     Also: it's analysis, and nobody has used the app long enough to have anything worth analyzing.
     Post-beta it may turn out to be two features instead of six.
REPLACES: "#8b — its own design session" (NEXT tier)
STATUS: LOCKED — parked
```

```
DECISION: Engine bundle shipped — adult auto-approval, daily recurrence/board fix, edit-form parity
DATE: 2026-07-10
WHY: Interlocking quest-lifecycle fixes bundled per hiatus sequencing; gives display mode a working stage.
STATUS: DRAFT — pending overnight verification of the daily respawn (due_date rollover across midnight).
        Adult auto-approval + completed-daily-leaves-board verified; edit-form parity shipped untested (low risk).
```

```
DECISION: roll_missed_dailies EXECUTE grant revoked from anon
DATE: 2026-07-10
WHY: Data-mutating recurring-quest reset function was anon-executable by default. Board calls it as a signed-in
     user, so authenticated retained. Verified: anon → 401; authenticated retains EXECUTE.
STATUS: LOCKED
```

```
DECISION: Approval-path RLS hardening confirmed necessary; fix scoped to Claude Code, not a Lovable patch
DATE: 2026-07-10
WHY: [REDACTED - mechanism held privately until patched. Restored post-fix.]
STATUS: LOCKED (finding) — fix is a Claude Code job. PRE-DISTRIBUTION BLOCKER.
```

```
DECISION: Kid-auth (username + PIN identities for kids) declined
DATE: 2026-07-10
WHY: Raised to fix the self-approval hole + kid-vs-kid impersonation. Declined because (1) it doesn't solve the
     approval hole — column-level RLS gating is still required regardless; (2) it taxes the load-bearing
     frictionless walk-up thesis; (3) impersonation is a profile-switch problem, not an auth problem, and is
     already answered by the optional kiosk-phase picture-lock plus the redeemer-name-and-face approval card.
STATUS: DECLINED — revisit only if the walk-up model itself changes.
```

```
DECISION: Vault kid view — dual-mode by affordability (catalog vs curated)
DATE: 2026-07-10
WHY: Curation fell through to an all-locked "wall of doors" when nothing was affordable (day one / just-spent) —
     precisely the moment a kid most needs to see the possibility space. Binary trigger: ZERO affordable → full
     catalog ("Wish Menu") grouped by tier, cheapest-first, favoritable (the goal-setting state); ONE+ affordable →
     curated (in-reach glowing + Redeem + next-unlock taunt + fold — the cash-out state). Favorites additive,
     carry between modes. Popularity/highest-redeemed explicitly rejected as a curation signal — it structurally
     buries the aspirational rewards, which are the ones doing the motivational work.
STATUS: LOCKED — shipped, both modes verified live.
```

```
DECISION: Vault adult view — approve + self-redeem + manage; reporting relocates
DATE: 2026-07-10
WHY: Three operational zones: (1) approve pending kid redemptions [redeemer name+face = impersonation mitigation],
     (2) the adult's OWN balance-anchored store reusing the kid store component (adults earn and spend embers too —
     core, not an edge case; adults redeem instantly), (3) reward-menu management [no redeem buttons].
     Reporting RELOCATES off this surface. Correction logged: an interim version wrongly stripped adult
     self-redemption — adults redeem via the same store component as kids.
STATUS: LOCKED (structure) — shipped; adult store curated flip unwatched.
```

```
DECISION: Couples reward rail = an audience filter on the reused adult store, not a separate build
DATE: 2026-07-10
WHY: Zone 2 reuses the kid store pointed at the adult's balance, making the long-parked adult-only tier nearly free:
     an `audience` flag on rewards (household / adults_only) + a viewer-role filter. Membrane-safe — only
     visibility changes; same sink, same flow. Migration backfilled existing rewards to household.
     This is the second use of the audience pattern (after quests), which promotes it to an architectural
     primitive: nullable enum + viewer-role WHERE clause, never a second screen.
STATUS: LOCKED — shipped, kid-side invisibility verified.
```

```
DECISION: Audience-scope badges are an adult-view legibility tool, across all three quest audience values
DATE: 2026-07-10
WHY: The quest audience flag has three values but was being handled asymmetrically (only adults_only reasoned
     about). Generalized: adult view badges scoped quests ("Adults only" / "Kids only"); anyone → no badge.
     Badges do NOT render on the kid board — a kid sees claimable work, and the distinction only matters to the
     adult setting it. Badges also make the filter self-legible for verification.
STATUS: LOCKED
```

```
NOTED — Free-daily-credit throughput floor (process)
DATE: 2026-07-10
OBSERVATION: 5 free daily Lovable credits ≈ one three-part interlocking build prompt (the engine bundle), ~0.8 left
     after. Not a same-day limiter — the throughput FLOOR for days when subscription credits are exhausted.
IMPLICATION: On free-only days, plan one meaningful bundled prompt/day; batch interlocking fixes ruthlessly; keep
     the highest-value bundle teed up first. Copy-only sweeps may run cheaper (measure).
STATUS: NOTED — single data point.
```

```
NOTED — Reward menu should floor low (fast first cash-out)
DATE: 2026-07-10
OBSERVATION: The live Vault floors at 25 embers, so a new kid stays in catalog mode until earning 25 — catalog is
     the guaranteed day-one state, and first redemption is gated behind that floor.
GUIDELINE: Ensure a genuine low Dim-tier reward exists, cheap enough that a kid's first quest or two clears it.
     A fast first cash-out teaches and sells the whole loop. Bake into starter-reward suggestions for new holds.
STATUS: NOTED
```

---

## 2026-07-04

```
DECISION: The tier ramp is DIM / WARM / HOT / BLAZING. "Legendary" is not a fifth tier.
DATE: 2026-07-04
WHY: The middle tiers were previously unnamed. Legendary is a campaign-completion OVERRIDE state on the feed —
     a significance flag, not a rung on the ladder. Conflating them would break the brightness=heat=importance
     ramp by adding a tier nothing can be authored at.
STATUS: LOCKED
```

```
DECISION: "Quest" is the universal object term. "Bounty" survives only as a proper noun.
DATE: 2026-07-04
WHY: The prior rule (assigned_to null = "Bounty"; assigned_to set = "Quest") created real friction — it confused a
     tester (Phaeaz) and Scott himself found it hard to apply consistently. Collapsing to "quest" universally, with
     Open/Assigned status badges carrying the state, fixes it. "Quest" being universally understood is a FEATURE,
     not a weakness — the differentiation budget is already well spent on ember, hold, hearth, Vault, Ranks.
     "Bounty" is retained ONLY in the Briefing's "Open Bounties" strip.
REPLACES: The assigned_to-null-means-Bounty naming rule.
STATUS: LOCKED — string audit across all UI surfaces still OWED.
```

```
DECISION: Adult reward Vault — 7-item v1 menu, individual redemption
DATE: 2026-07-04
WHY: Adults earn and spend embers too; May is actively using the app both to test and to gain visibility into her
     own household contributions. Menu across the tier ramp: Dim — coffee/breakfast delivered, pick dinner;
     Warm — skip one chore-quest, pick movie/show night; Hot — one guaranteed hour alone; Blazing — solo morning
     routine, solo bedtime. DISHES EXCLUDED: it already exists as a quest, and the same chore being both source and
     sink reads as a bug. DATE NIGHT RELOCATED to Campaigns (EARNING type): it's a shared goal two people pool
     toward, not something one person buys — which is exactly what the Vault deliberately doesn't do.
STATUS: LOCKED
```

---

## 2026-07-03

```
DECISION: Feast → HALL (rename AND redefinition)
DATE: 2026-07-03
WHY: "Feast" narrows to food — a generator fed the word returned 6/8 food characters. "Hall" reframes the class as
     the whole gathering hall: makers AND entertainers AND keepers of warmth (bards, storytellers, dancers,
     revelers, hearth-tenders, hosts), with food ≤ ~1/3 of the class. Also restores the spatial-noun logic of the
     other three (Forge / Garden / Keep). "Hall" over "Hearth" by Scott's call. Cheap find/replace: label, enum
     value, animation-theme name.
REPLACES: The open Feast→Hall/Hearth question carried since 6/29.
STATUS: LOCKED
```

```
DECISION: Avatar roster expands to 10 per class (40 base) + 4 cool-register = 44
DATE: 2026-07-03
WHY: The collapsible per-class picker already makes depth free, so every "swap or cut" argument dissolves into
     "keep both." Free tier stays 4-per-class (16); Founding Guildhall now unlocks 24 instead of 16 — a stronger
     paid offer with the free tier's completeness untouched. The 4 cool-register characters (one per class) complete
     the register spread rather than forming a standalone set — which also dissolves the "all eight are the same
     blue" cohesion problem, since each cool character now matches its class palette.
STATUS: LOCKED — finer free/paid split TBD.
```

```
DECISION: The avatar generation method — axis control, one-run rosters, material-and-light for the cool register
DATE: 2026-07-03
WHY: Hard-won through a full day of failure modes. Full method in playbooks/avatar-generation.md. Summary:
     (1) the style block controls output by AXIS, not LENGTH — pin the floor, free the casting/palette/pose;
     (2) generate the FULL roster in ONE run so the cross-roster anti-repeat constraint can actually bind;
     (3) the cool/eerie register must be prompted through MATERIAL and LIGHT, never supernatural vocabulary
     (content filters flag the semantic field by noun density and ignore disclaimers);
     (4) circular-crop-safe framing is a permanent composition rule;
     (5) committed-cute-per-class — generators underweight the cute end and regress to one token per class.
STATUS: LOCKED
```

```
DECISION: The Copilot Create bridge is a validated part of the working model
DATE: 2026-07-03
WHY: Copilot designs against the locked floor → Scott selects → Lovable engineers. Same lane discipline, different
     asset engine, zero Lovable-credit burn (work account). Proven end-to-end across a 44-character roster.
STATUS: LOCKED
```

---

## Earlier

```
DECISION: Monetization — a single v1 SKU, the "Founding Guildhall," at HOUSEHOLD level
DATE: (pre-hiatus, confirmed through 7/03)
WHY: THE SEAM RULE. Per-class or per-character SKUs recreate a "buy each kid their skin" dynamic INSIDE a household
     tool — the worst possible seam to place between a parent and their kids. A household-level unlock dissolves it
     entirely: one purchase, everyone gets everything. One-time, not subscription. Stripe Checkout + Supabase
     webhook + a household entitlement flag; staying off app-store rails retains ~97% of revenue.
     Free tier keeps 16 avatars (4/class) and must be GENUINELY COMPLETE, not a demo — the membrane applied to
     money: free is a full tool, the purchase is delight, never access to basic function.
     ALL FUTURE COSMETIC DROPS MUST REMAIN HOUSEHOLD-LEVEL. No exceptions.
STATUS: LOCKED
```

```
DECISION: The activity feed is append-only and permanent — cap the READ, never the WRITE
DATE: 2026-06-29
WHY: The activity_log is the source of truth for everything that has happened in the hold, and the eventual source
     of the Adventure Log. Consumers window their own reads. No pruning queue, ever.
STATUS: LOCKED
```

```
DECISION: The membrane — the game lives in quests only
DATE: (foundational)
WHY: Calendar, Lists, meals, and notes stay clean utilities that may OPTIONALLY spawn a quest. Gamify the chore,
     not the grocery item. Gamifying everything is how a master becomes a jack. Lists writes nothing to the feed
     spine — a deliberate proof of the rule.
STATUS: LOCKED
```

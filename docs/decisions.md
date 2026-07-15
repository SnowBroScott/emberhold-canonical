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
# APPEND TO `docs/decisions.md`

*Paste these at the top of the current-decisions section. `decisions.md` is append-only.*

---

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

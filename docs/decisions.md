# Decisions
**Append-only.** Newest at top. A decision here is design truth; the *implications* get folded into `master-spec.md`.

**Status tiers:** `LOCKED` (decided, don't relitigate) · `NOTED` (observed, informs future calls) · `DRAFT` (leaning, pending verification) · `DECLINED` (considered and rejected — recorded so it stays rejected)

**Format:**
```
DECISION: [what was decided]
DATE: [date]
WHY: [the reasoning, including rejected alternatives]
REPLACES: [what this supersedes, or: Nothing — new decision]
STATUS: [LOCKED / DRAFT / NOTED / SUPERSEDED / DECLINED]
```



---
DECISION: Stripe, the Founding Guildhall, and all money-and-paperwork work is PHASE 2, explicitly after a complete and ready Emberhold. "Ready" means Gate B's exit criterion and nothing more.
DATE: 2026-08-02
WHY: The critical path carried "Founding Guildhall build (Stripe + webhook + entitlement)" as a single row. It is not a row. It is Stripe account setup, a checkout surface, a webhook endpoint with no existing infrastructure, an entitlement write with an RLS story, refund posture, tax posture, COPPA, and a privacy policy that must name flock.js. Gate D is a landing page, an install tutorial and PostHog, none of which exist. Both were sitting on the board as nouns rather than as work, which made the board look thin on a no-credit day and hid how much is actually left. Splitting them out does two things: it gives Phase 1 a finish line that is achievable and testable, and it forces Phase 2 to be decomposed before it is estimated. REJECTED: leaving them inline on one ladder, because a row that is fifteen tasks corrupts every estimate downstream of it. REJECTED: decomposing Phase 2 tonight, because it is its own session and doing it badly at the end of a long one is worse than not doing it. NOTE: the finish lines are different. The feature loop is done and Emberhold is close to the end of BUILDING. It is a long way from done. Those were being treated as one thing.
REPLACES: The single-ladder framing where Gate C sat inline between B and D.
STATUS: LOCKED

---

DECISION: bun is this project's package manager. npm and yarn must never be run in this repo, and the prohibition is a named line in every Code brief.
DATE: 2026-08-02
WHY: bun had never been installed on Scott's machine at all. Every Code job this project has ever run silently fell back to npm, resolving against a package-lock.json that is not this project's lockfile. Consequences, all discovered tonight: a stale package-lock.json was sitting untracked in the working tree for weeks; one job resolved @tanstack/react-router to 1.170.17 against bun.lock's pinned 1.170.16; and every npm audit result ever recorded in canon was a statement about the wrong dependency tree, which means "0 vulnerabilities" was never a claim about what ships. bun 1.3.14 is now installed, package-lock.json is deleted, and the tree is restored via bun install. REJECTED: treating this as a one-time cleanup, because the failure was silent and repeated nine times without anyone noticing, which is exactly the profile of a thing that needs a standing brief line rather than a memory. The general lesson: verify the tool is present, not just that the convention names it.
REPLACES: Nothing. New decision.
STATUS: LOCKED

---

DECISION: The 47 TanStack Router typecheck errors are real and pre-existing, and they will not be fixed before Phase 1 closes.
DATE: 2026-08-02
WHY: The hypothesis that they were an artifact of the stray npm install was tested and DISPROVED: identical error count under npm's 1.170.17 and bun's 1.170.16. All 47 are one class, a missing `search` property on navigate, redirect and Link calls, with 36 matching that string literally. They are type-layer only with zero runtime impact, and every affected path is exercised on the glass daily. Fixing them means touching 36 call sites across effectively every route in the application for zero user-visible change, which is a large diff with real regression surface in exchange for a clean typecheck. That is not pre-stranger work. REJECTED: fixing them now for the sake of a clean build output, because the same clean-output goal was achieved by the one-line vitest fix, which is what was actually burying signal. Reopen only if a router upgrade forces the issue. NOTE FOR FUTURE JAINE: eleven consecutive Code jobs reported the single vitest error and none of them mentioned the other 47. That is a fact about how agents summarize typecheck output, not about the codebase, and it is worth distrusting the next "one pre-existing error" report accordingly.
REPLACES: Nothing. New decision.
STATUS: DECLINED

---

DECISION: quests.approved_by is validated by extending the enforce_quest_family_refs trigger, not by building an approve_quest RPC and rewriting five call sites.
DATE: 2026-08-02
WHY: The brief said to copy approve_redemption's shape. The stop-clause fired, correctly: there is no quest-approval RPC to add an optional actor parameter to. Five client surfaces write approved_by with a direct UPDATE (quest.$id.tsx twice, wall.tsx, slate.tsx, quest-log.tsx, Briefing.tsx). The pattern worth copying is the PRINCIPLE, "the database validates the actor rather than trusting what the client sends," not the IMPLEMENTATION, "an RPC takes an optional validated id." approve_redemption needed an RPC because it already had one and the client was discarding a resolved identity on the way in. Here all five sites send the id correctly and nothing checks it on arrival, so a trigger checks it on arrival and covers all five at once with zero client changes. REJECTED: option B, a new approve_quest RPC with five rewritten call sites. It is the faithful pattern match and the wrong trade: same security outcome, but it puts the Briefing's claimed_by credit-reassignment and the wall's PIN-verified approve flow inside the blast radius of a security fix, on 3.1 remaining credits. It reopens only if there is an independent reason to centralize approval, which there currently is not. INVARIANT HELD: no auth.uid() comparison anywhere in the change. A switched-to adult profile is a valid approver and remains one, because requiring actor = auth.uid() would break approval from a switched profile, which is normal daily use. NOTE: all 88 pre-existing approvals already named active adults. The hole was open for the feature's entire life and nobody fell in it.
REPLACES: The framing in status.md that called this "the fourth site of the validated-actor-id RPC pattern."
STATUS: LOCKED

---

DECISION: public.system_flags is accepted as readable by any authenticated user, on the condition that the read policy is narrowed before any non-public or non-boolean flag is added to it.
DATE: 2026-08-02
WHY: Lovable's scanner flagged "System flags readable by any authenticated user." Recon identified the surface: one table, columns key/value/updated_at, RLS enabled, policy FOR SELECT TO authenticated USING (true), holding a single global row ('founder_gate_enabled', false). anon has no grant and no policy and cannot read it at all. There is no family_id column and no per-tenant data, so every authenticated user reads the same global false. That is not a leak and it is not a tenant-isolation issue. THE CONDITION IS THE POINT: the table is a bare key/value store with nothing constraining what goes into it. A Stripe live-mode switch, a rollout percentage, or a kill switch inserted here would become world-readable to every account, silently, with no code change and no review. This is written down so that a future agent building Phase 2 does not cheerfully insert one. REJECTED: narrowing the policy now, because there is nothing to protect yet and a migration spent on a hypothetical is a migration not spent on a real item. REJECTED: leaving the finding un-triaged on the Lovable panel, because an unexplained warning trains the reader to skim the panel.
REPLACES: Nothing. New decision.
STATUS: NOTED

---

DECISION: sandbox_exec's EXECUTE grants stay. Do not revoke them.
DATE: 2026-08-02
WHY: Lovable confirmed it is a platform-managed role: the internal execution role for the agent sandbox, the migration runner, and schema introspection, created when the Lovable Cloud backend is provisioned. Critically, the postgres role is a member of sandbox_exec WITH ADMIN OPTION and inherits its privileges. Revoking would break Lovable Cloud migrations, agent introspection, and the postgres to sandbox_exec privilege chain. REJECTED: manual revocation, which was the open question on the board for weeks and would have been an outage. If the grants are ever to be narrowed, that is a Lovable support conversation, never a hand-rolled revoke. This closes a security-triage item that has sat unexplained since the grant surface work on 07-21.
REPLACES: The open "ask Lovable what sandbox_exec is, one question" item.
STATUS: LOCKED

---

DECISION: No setup value is stashed client-side before there is a verified session. Email and password only, until email confirmation produces a real session to write with.
DATE: 2026-08-02
WHY: This rule already governs the code. It was written as a prose comment at auth.tsx lines 22 to 24 during the 07-26 signup rebuild and never made it into canon. It is being captured now because the PIN recon proved it is load-bearing: the entire application writes exactly two localStorage keys, emberhold:active-member and emberhold:lastSection:{listId}, and the adult PIN lives in React state during form entry and goes only to set_profile_pin over HTTPS. Lovable's scanner still reports the pre-rebuild behavior, which means the rule is the thing that made the finding false. A design rule living only in a source comment does not survive the next rewrite of that file, and the next agent to touch signup would have no reason to know it was deliberate rather than incidental. STATE LIVES IN THE REPO OR IT DOES NOT EXIST, applied to a rule rather than to a plan.
REPLACES: Nothing. New decision, retroactively capturing a 07-26 behavior.
STATUS: LOCKED

---

DECISION: The kid joiner walk is downgraded from a pending verify to a will-surface-itself item. Stop asking Scott to run it.
DATE: 2026-08-02
WHY: Scott's call, and the reasoning holds. The joiner flow is adult-supervised at the exact moment it runs, and an adult standing over a kid's shoulder is a live debugger with a phone. The failure mode the verify was protecting against is silent failure with nobody watching, and this path is never walked unwatched. Additionally, Mia cannot read and Cade is a seasoned user, so neither is the naive stranger the walk was meant to simulate. REJECTED: continuing to carry it as a Gate B blocker, because it had been raised in four consecutive sessions and repeated asking was costing attention without changing the risk. NOTE, AND THIS IS THE GENERAL LESSON: the board cannot distinguish "never done" from "done so often nobody narrates it." Three items were carried as pending verifies tonight that turned out to be routine daily life, including kid redemption, which had been listed as unproven since 07-31 while Scott's kid was using it regularly. This is the rumor problem inverted and it is more expensive: a rumor wastes one session, a stale verify wastes attention every single time the board is read. THE FIX IS FREE: jAIne asks before carrying an item forward, and Scott answers normal, never happened, or don't care.
REPLACES: The kid joiner walk's standing as critical path #4.
STATUS: NOTED
---
DECISION: The service worker ships for installability only and caches nothing. The offline shell is deprioritized indefinitely.
DATE: 2026-08-01
WHY: Android Chrome will not offer an install prompt without a registered service worker carrying a fetch handler; iOS does not require one, which is why the iPad installed and the tablet never did. The worker that satisfies this is eighteen lines with zero references to the Cache API and an empty fetch handler. Caching is a separate decision and was rejected on its own merits: household wifi is pervasive, a chore board is not what anyone needs in a dead zone, and the cache strategy carries essentially all of the risk for almost none of the benefit. A caching bug is the one defect on this board that cannot be fixed by pushing a fix, because the stale worker serves the fix's own replacement. REJECTED: vite-plugin-pwa and Workbox, because a plugin generates a precache manifest, which is exactly the artifact being deliberately not built. REJECTED: a pass-through fetch handler calling respondWith(fetch(request)), which is not a pass-through, breaks range requests and re-wraps redirects. skipWaiting and clients.claim are included deliberately as a kill switch: default update behavior parks a new worker in "waiting" until every tab closes, which on a wall tablet that never closes is functionally never. A no-cache worker without a kill switch is a worse trade than no worker. STANDING CONSTRAINT: any future caching work must never cache a response carrying an Authorization header, which would reintroduce tenant isolation at the cache layer on a boundary verified under live attack. The offline shell reopens if PWA push is ever built, because they are the same mechanism.
REPLACES: The Gate B framing of the service worker as offline-shell polish, in status.md and parking-lot.md.
STATUS: DRAFT — contingent on the Android install prompt actually appearing. The worker is registered and running; the prompt has not been seen. If Chrome still withholds it, the criterion was not the only one missing and this entry gets amended rather than promoted.

---

DECISION: /quest-log and /hearth-log stay until Gate C, and the QUEST APPROVED enum leak on /hearth-log is accepted rather than open.
DATE: 2026-08-01
WHY: Ratifying in canon what was reclassified in conversation on 08-01 and folded into master-spec tonight. These are a deliberate debug surface with a live consumer, Scott, and they earned their keep twice in two days as the readable record of what the feed actually wrote. The old gate, "delete once the Slate is trusted," was wrong because it assumed doubt was the only reason to keep them. Their copy stays deliberately unswept so it remains obvious which surface is which. REJECTED: fixing verbLabel(), which would cost a job to change a string reachable only on one unlinked page. ACCEPTED COST: an unswept vocabulary surface will eventually say "quest" while every other screen says bounty, and someone will read it at 11pm and believe it. They are removed at Gate C alongside prod test-object cleanup, scheduled rather than deletion-pending.
REPLACES: "Route deletion is a follow-up, after the Slate is trusted."
STATUS: LOCKED

---

DECISION: Page titles use a middot. The Slate's collapsed group is labeled "{n} done."
DATE: 2026-08-01
WHY: Two copy calls landed together. Page titles: the 08-01 em-dash sweep turned "Board — Emberhold" into "Board: Emberhold" across 32 route files plus __root.tsx's title, og:title and twitter:title. A colon implies the left side contains the right, and it was a form nobody specified; the sweep chose it by default. Now "Board · Emberhold" across 38 strings in 34 files. The Slate label: the collapsed group reads "{n} more current" and contains only rows completed for the current occurrence, so "current" said the opposite of what those rows are. REJECTED: "{n} done, back Aug 3", because the rows carry their own return dates and a group label repeating one is copy explaining something the screen already shows, and because a mixed-cadence view has no single date to name. No pluralization branch; "1 done" and "5 done" both read.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: member_admitted and member_denied render as named verbs on all three feed surfaces.
DATE: 2026-08-01
WHY: The activity_verb enum has seven values and every renderer named five, so a server-written admission rendered as a bare "Mom · Leo" with no verb at all. Now "${who} admitted ${obj}" and "${who} denied ${obj}" in wall.tsx, Briefing.tsx and NotificationBell.tsx, matching the existing past-tense shape. CONSIDERED AND REJECTED: leaving member_denied unrendered, on the argument that a permanent feed entry for a rejection is a household conversation rather than a system message, which is the reasoning behind the existing DECLINED on telling a joiner what role they were confirmed as. Scott called it fine as written. hearth-log.tsx was deliberately excluded as the kept debug surface. NOTE: member_denied has never rendered in production because nobody has been denied; its first real render will be its first test.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: A brief that prescribes an action should carry a stop-clause naming the condition under which the action is wrong.
DATE: 2026-08-01
WHY: The blurb brief said delete this string, and added: if this turns out to be an empty state rather than a persistent blurb, stop and report instead. It was an empty state. Deleting it would have left a stranger with a blank section and no guidance, which is the opposite of the rule that motivated the deletion. The clause cost one sentence to write and converted a wrong brief into a finding. This generalizes: jAIne briefs from descriptions in status.md rather than from source, and a description is a claim about a file, not the file. The stop-clause is the cheap hedge that does not cost execution latitude, because it only fires on a named condition rather than inviting the agent to second-guess generally. COROLLARY: the delete-don't-reword copy default has an exception, and it is empty states. An empty state has no content to be redundant against.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The three verb-to-display switches are not byte-identical, and canon carried the overstatement for one session.
DATE: 2026-08-01
WHY: The 08-01 recon was briefed to check whether the verbLabel raw-enum fallback existed in wall.tsx, Briefing.tsx and NotificationBell.tsx. It answered that question correctly: the five named verbs behave identically and the default never touches row.verb. jAIne then reported them as "word-for-word the same switch" and folded that into master-spec. Code found the drift while adding the two new cases: wall.tsx's event_created reads "${actor} added ${obj}" while the other two read "${actor} added ${obj} to the calendar". This remains duplication rather than divergence and is still not the isActiveQuest failure mode, since nothing here is a correctness bug. But three copies that have already drifted once is a materially different claim from three that have not, and the shorter string is on the wall, which is the semi-public surface. THE TRANSFERABLE LESSON: a recon answers the question it was briefed with. Do not generalize its finding to the container the question lived in.
REPLACES: The "word-for-word identical" characterization in the 08-01 recon summary and in the master-spec fold delivered earlier this session.
STATUS: NOTED
---
DECISION: The offline shell drops to LATER; installability is promoted to a Gate B blocker
DATE: 2026-08-01
WHY: Gate B has carried "service worker" as one item with one justification — an offline themed shell instead of Chrome's dinosaur. Setting up the permanent wall tablet split that item in two and inverted their priorities. Android Chrome will not offer to install Emberhold, the manifest is valid and serves correctly, and the remaining installability requirement Chrome enforces is a registered service worker with a fetch handler, which has never been built. iOS enforces no such thing, which is why every install we have ever done worked and hid the problem. So a stranger on the non-Apple half of the market cannot install the app at all, and Gate B's real exit criterion is that they can — not that an offline screen looks nice. NOT YET CONFIRMED: nobody has checked whether an SW is registered at all; Lovable's stack may have shipped one. THE OFFLINE SHELL ITSELF IS DEPRIORITIZED DELIBERATELY, on Scott's call: household wifi is effectively pervasive, a chore board is not the thing anyone needs in a dead zone, and a cache strategy carries the single failure mode on this board that cannot be repaired by pushing a fix, because the broken mechanism is also the delivery mechanism. Rejected: building both together as one careful pass, which was the standing plan and which welds a low-value feature to a blocking one. Rejected: declining the offline shell outright — it reopens automatically if PWA push is ever built, since push and service workers are the same mechanism. CONSEQUENCE: the minimal SW is now SAFER than the version canon feared, because caching nothing leaves no stale state to trap a user in. Hard invariant regardless: never cache a response carrying an auth header, which would reintroduce tenant isolation failure at the cache layer.
REPLACES: The Gate B framing of the service worker as offline-shell polish
STATUS: DRAFT — contingent on confirming no service worker is currently registered
---

DECISION: /quest-log and /hearth-log are a kept debug surface, removed at Gate C, not deletion-pending
DATE: 2026-08-01
WHY: The standing plan deleted both routes "once the Slate is trusted." The Slate is now trusted and the routes stay anyway, because the gate was wrong: it assumed the only reason to keep them was doubt about their replacement. The Hearth Log is a working debug surface with a live consumer — Scott — and it earned that twice in two days as the readable record of what the activity feed actually wrote, including the wall's missing logActivity write. Rejected: deleting them now, which would have killed the verbLabel enum leak for free but cost a real diagnostic tool at the exact moment we are still finding feed bugs. Rejected: keeping them indefinitely, which leaves an unswept vocabulary surface reachable in production. The scheduled removal joins prod test-object cleanup on the Gate C shelf — both exist because we are still building, and both come out immediately before a stranger can reach the product. ACCEPTED COST: the Hearth Log is deliberately unswept, so it will eventually say "quest" while every other screen says bounty, and someone will read it late at night and believe it.
REPLACES: The 2026-07-30 posture that route deletion follows once the Slate is trusted
STATUS: LOCKED

---

DECISION: Fixing the verbLabel enum leak is DECLINED
DATE: 2026-08-01
WHY: hearth-log.tsx's verbLabel() special-cases only bounty_posted and falls through to verb.replace("_"," "), rendering QUEST APPROVED. Read-only recon on 2026-08-01 quoted the source of all three surviving surfaces — wall.tsx's tickerLine, Briefing.tsx's pulseLine, NotificationBell.tsx's lineFor — and proved they are word-for-word the same switch, five named verbs plus a default returning `${who} · ${obj}` that never touches row.verb. hearth-log.tsx is therefore the only place in the codebase that derives display text from a raw enum value, and it is an unlinked page reachable only by clicking through the board feed. A fix would spend a job changing a string nobody encounters by accident, on a file scheduled for removal. Rejected: fixing it anyway for tidiness. Rejected: deleting the file to moot it, which is the same trade with a worse side effect (see the entry above). BONUS FINDING: activity_verb has seven values and every renderer names five; member_admitted and member_denied fall to the label-only default, which is why the feed reads "Mom · Leo." That is two missing cases in three identical switches, not a display bug — a cheaper fix than the polish list assumed.
REPLACES: Nothing — new decision
STATUS: LOCKED

---

DECISION: No em dashes in user-facing copy, and none in briefs handed to Lovable
DATE: 2026-08-01
WHY: Scott flagged the em dash as the current strongest tell that copy was machine-written. The mechanism is already documented for design-rationale phrasing — jAIne writes a brief, Lovable renders jAIne's phrasing as UI — and punctuation escapes by the identical route, which is why the rule has to cover the brief and not just the glass. The 08-01 sweep removed 125 em/en dash characters across 43 files. Rejected: treating this as a one-time cleanup, because the source is jAIne's writing habit and it will re-offend on the next build prompt. BOUNDED, and these stay: en dashes in time and date ranges are correct typographic convention rather than the tell, and a bare — used as a "nothing set" placeholder is a display value, not prose. Also unchanged: user-authored content is out of scope for every sweep. SECOND-ORDER EFFECT WORTH KNOWING: removing a dash sometimes removes structure, not just punctuation — slate.tsx's standing-duties parenthetical now sags as commas and needs a second look.
REPLACES: Nothing — new decision
STATUS: LOCKED

---

DECISION: One resolver, not one expression copied twice — and the validated-actor-id RPC is now the standard for the profile-id-vs-auth-id class
DATE: 2026-08-01
WHY: The first-run marker's read and write were both scoped to the account holder in different ways: mark_first_run_complete() wrote WHERE id = auth.uid(), markFirstRunComplete contained a literal void profileId that discarded the id it was handed, and FirstRunGate read a route-context profile fetched WHERE id = data.user.id. Three points of failure, and the two that cancelled meant fixing either alone produced an infinite first-run loop. The fix drops the zero-arg function outright — not a defaulted parameter, which would leave the old signature callable as an overload — and creates mark_first_run_complete(_profile_id uuid), SECURITY DEFINER, validating household membership and active status server-side without ever comparing against auth.uid(). That is the third shipped instance of this pattern after approve_redemption and wall_request_redemption, and it is now the default answer whenever a caller correctly resolves an identity and the callee discards it. The frontend half went further than specified: rather than writing the same resolution expression in two files, both readers call one shared resolveFirstRunProfileId(). Two identical copies of an expression is how isActiveQuest and the four verb renderers got where they are; a single resolver cannot drift. Rejected: fixing the read alone. Rejected: a defaulted parameter. VERIFIED ON THE GLASS by switching to a kid, completing first run, and switching back — the second entry is the test.
REPLACES: Nothing — new decision
STATUS: LOCKED

---

DECISION: Plan mode for every migration, reviewed in ONE pass — and invariants stated as explicit lines in the brief
DATE: 2026-08-01
WHY: Plan mode was correct for the marker migration because a code revert is not a database revert: a wrong migration is not undone, it is followed by a second migration, which costs a second spend on top of the first. Plan mode makes the schema decision reviewable before it is a fact. THE COST DISCOVERED: a plan iteration is billed like a build. Two round trips over one constraint consumed two of five credits, and the whole build came to roughly 2.8. Therefore the review is a single pass — every objection goes in one message or it goes in the next session. THE DEEPER MISS: the constraint that drove both iterations was that FirstRunGate's null fallback must match the writer's. jAIne wrote "the read and the write must resolve identically" as prose intent and left the fallback implicit, then bought it back at a credit per turn. Loose briefs remain correct for execution latitude — Scott's best avatars came from them and the over-specified ones were weakest — but an invariant is not latitude. The thing that must be true gets its own line.
REPLACES: Nothing — new decision
STATUS: LOCKED

---

DECISION: Same-row roll-forward is verified for monthly and the submitted exclusion works — and "last done" is what makes a moving row auditable
DATE: 2026-08-01
WHY: August 1 read five monthly rows, all MONTHLY · 1ST, all Due now, zero duplicates, no archive-and-spawn. The throwaway daily created and claimed the previous evening sat unmoved at July 31 still awaiting approval ten hours later, which is the only condition that exercises the submitted exclusion — a row past its anchor with somewhere to move. Both legs pass. THE CONTROL PARTLY FAILED AND THE PRODUCT SAVED IT: the banked discriminator was Cade's claims on two rows, and Cade actually did those chores overnight, so approval moved the rows before the rollover did and the claim evidence went with it. The board stayed legible only because each row carries "last done Jul 31." A row that moves instead of respawning has no history unless it carries one, and that field turned an ambiguous morning into a self-explaining one without a query. It was designed as a convenience and it is load-bearing. LESSON FOR FUTURE CONTROLS: a snapshot taken from a live household can be consumed by the household. Prefer a control the working product cannot erase, or make the row carry its own history. STILL OPEN: this is an incidental exercise of the monthly branch, not its verification — successor arithmetic and roll-forward are different tests and the clean natural one is 2026-09-01.
REPLACES: Nothing — new decision
STATUS: NOTED
---
DECISION: status.md's own stale-doc flag was stale. A tracking item outlived the work it tracked by a full day.
DATE: 2026-07-31
WHY: status.md carried "master-spec.md owes a full regeneration — 669 lines, six items stale, deferred twice, first in the queue" through an entire session. It was wrong on every count. The doc was folded on 2026-07-30 (late), which landed the Bounty rename, the Slate and the Ledger; it stood at 736 lines, not 669; and the fold was not owed. jAIne read that flag at session open, never fetched the file, and copied both the number and the verdict into status.md, parking-lot.md and a session-close reconciliation line — declaring "master-spec: no change" about a document she had not opened. Scott caught it with two facts: it was updated yesterday, and the line count is wrong. THE ACTUAL DEFECT WAS THE INVERSE OF THE ONE TRACKED, WHICH IS WHY IT SURVIVED. master-spec was not missing decisions; it was retaining superseded ones. Nine lines described decided, shipped things as open — most dangerously the Ledger's "ITS SHAPE IS NOT DECIDED... do not build the Ledger from this section alone," sitting in a LOCKED doc while the Ledger was live in production. A fresh instance reading that would decline to build a surface that already exists. THE RULE: A TRACKING ITEM IS A CLAIM ABOUT A DOCUMENT, NOT THE DOCUMENT. Verifying it costs one fetch. This is the same failure as reasoning off north-star's stale wall line, twice in one session, both by trusting a doc's description of another doc instead of reading the other doc. State lives in the repo — but so does staleness, and a status board is exactly as capable of rotting as the thing it tracks. Corollary: a fold and its status-board entry must be updated in the same commit, or the entry starts lying immediately.
REPLACES: Closes the "master-spec.md owes a full regeneration" item carried in status.md and parking-lot.md since 2026-07-29.
STATUS: NOTED
---
DECISION: The Vault's redemption request routes through wall_request_redemption. The direct client insert is deleted.
DATE: 2026-07-31
WHY: A sub-profile tapping Redeem on the Vault page got "new row violates row-level security policy for table redemptions." The cause was not a regression: the redemptions INSERT policy has required requested_by = auth.uid() since 2026-06-28, and in the shared-session model auth.uid() is ALWAYS the household owner regardless of which profile is switched to client-side. The Vault's insert set requested_by to the active member's profile id. Those two values can only match for the owner, so the path had failed since the day it was written and nobody had ever exercised it — every prior redemption test was run as an adult. The wall's identical-looking redemption worked because wall_request_redemption is SECURITY DEFINER and bypasses table RLS entirely, checking only that the profile belongs to the household and is active. TWO INDEPENDENT IMPLEMENTATIONS OF ONE INSERT, AND ONLY ONE OF THEM WAS EVER CORRECT. The fix points the Vault's kid branch at the RPC the wall already used: frontend-only, no migration, zero Lovable credits, and it deletes the duplication rather than adding a third variant. REJECTED — the recon's own recommendation, which was to tighten wall_request_redemption to enforce the same self-attribution the RLS policy encodes. That would have forced _profile_id = auth.uid(), meaning only the household owner could ever redeem from the wall. The recon treated the older policy as correct and the newer RPC as deviant; it is the other way round — the policy compares a profile id against an auth id, an assumption the sub-profile model outgrew. REJECTED — changing the RLS policy, which is a migration, needs credits that did not exist, and would have widened a security surface to fix a frontend routing mistake. KNOWN DEBT, DELIBERATE: wall_request_redemption is now called from the Vault and its name lies. Renaming an RPC is a migration; rename it when something else takes that function to Lovable. Verified on the glass: Mia requested from the Vault page, no error, "Request sent to an adult," row landed pending, balance held at 61 until approval, then moved.
REPLACES: Nothing — new decision. Fixes a path that had never worked.
STATUS: LOCKED

---

DECISION: The wall's approve mutation writes its own activity_log row. Server-side logging is raised as an open question, not adopted.
DATE: 2026-07-31
WHY: The wall's approve path called the shared approve_redemption RPC and never called logActivity; vault.tsx's approve path always did. So every redemption ever approved from the wall was invisible to the feed, the ticker and the Hearth Log — embers moved and nothing recorded it. This was not a failing or conditional call; the call was simply absent, and had been since the wall shipped. The immediate fix mirrors vault.tsx at the one missing site: one line, free, verified on the glass (approved Extra screen time from the wall, "Mia redeemed Extra screen time · -25" on the ticker, balance 61 → 26 across two redemptions). THE MECHANISM IS THE REAL FINDING AND IT IS NOT FIXED: logActivity is a client-side call hand-bolted onto each call site, so every new site can forget, and one did, silently, for weeks. The mechanism fix is the definer RPC writing its own log row — but that is a migration, and it is not obviously right, because four surfaces already render their own display lines from the verb enum and the label may not belong in the database at all. Recorded as an open decision in parking-lot rather than decided here. NOTED FOR THE NEXT TOUCH: the fix went into mutationFn rather than onSuccess, so a throwing log write would report a failed approval that actually committed. Not verified against how vault.tsx does it.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: A bright-heavy Slate is honest. The open question is closed and the "watch it for a week" instruction is cancelled.
DATE: 2026-07-31
WHY: The Slate opened at "13 need doing" after roll-forward moved every stale weekly to the current anchor, and 07-30 parked the question of whether that reads as honesty or as pressure, with an explicit instruction not to touch it reactively and to live with it for a week. Scott lived with it for a day and called it: it looks and feels fine. Closing it now rather than running out an arbitrary clock, because the clock was jAIne's invention and the criterion was always Scott's eye. THE PREMISE THAT NEEDS CORRECTING IS THE MORE USEFUL HALF: the 07-30 design note claimed "a dim Slate is a well-kept hold," which framed brightness as a health score and made a bright Slate a failure state. It is not. The Slate's job is an accurate count of what is live; whether that count is comfortable is not its problem. A hold with thirteen things genuinely due should look like a hold with thirteen things genuinely due. If a Monday ever spikes it past comfortable, the lever is grouping or a softer due-now treatment — never re-hiding work, which is the duplication complaint that created the surface.
REPLACES: Closes the parking-lot OPEN DECISION "IS A BRIGHT-HEAVY SLATE HONEST OR OPPRESSIVE?" (2026-07-30). Corrects the 2026-07-30 Slate entry's claim that a dim Slate is the healthy render.
STATUS: LOCKED

---

DECISION: UI copy that explains something the screen already shows gets deleted, not reworded. The screen copy pass runs as a review inventory.
DATE: 2026-07-31
WHY: Scott flagged the Slate's subtitles — "EVERYTHING WITH A FUTURE," "RECURRING DUTIES — ONE LINE, FOREVER," "HERE UNTIL THEY'RE APPROVED" — as reading like the app explaining itself to itself rather than to a user. He is right and the mechanism is specific: those are jAIne's own design-rationale phrasings from the build brief, which Lovable rendered as UI. jAIne wrote the WHY into the brief and the WHY landed on the glass. This is a structural hazard of the jAIne → Lovable pipeline, not a one-off, and it will recur on every surface built this way. THE RULE: delete if the copy explains something already visible; reword only if it teaches something invisible. Under that rule three of four Slate strings were deleted outright — the section headers plus the recurrence chips already say what the subtitles said. THE COUNTER-EXAMPLES ARE THE CALIBRATION AND THEY STAY: the Vault's "Each request goes to an adult" and the wall's "AN ADULT APPROVES ON THEIR PHONE. NO EMBERS MOVE YET." Both teach the approval gate, which is invisible and is the thing people get wrong. Scott's call on the One-offs subtitle is the sharpest version of the rule: a subtitle explaining a deliberately plain header is an admission the header isn't plain enough. The Slate's eyebrow became EVERYTHING THAT'S LIVE — Scott's line, chosen over jAIne's longer "EVERYTHING YOUR HOLD HAS GOING." jAIne objected that Retired sits on the Slate and isn't live; Scott overruled on the grounds that the Retired section is dimmed and dim already means "not the subject of this screen," which is the brightness principle doing the work copy would otherwise have to do. TRACKED AS AN INVENTORY, NOT A BACKLOG ITEM: parking-lot carries a screen-by-screen table with review state. Slate and Ledger are reviewed; everything else is not. Batch before firing — four strings is not worth a Code job alone.
REPLACES: Nothing — new decision. Establishes the copy-pass discipline and its rule.
STATUS: LOCKED

---

DECISION: Brightness on the Slate means STATE and nothing else. The ember tier ramp does not drive row heat there.
DATE: 2026-07-30
WHY: The Slate was specced to run two brightness signals on one row — the ember value's DIM→WARM→HOT→BLAZING ramp and the row's live/handled state — on the reasoning that the board already does this. Scott killed it. The board can carry two signals because the ember number IS the point there; the Slate's entire health read is "is this hold well-kept," and a dial that means two things cannot answer one question. The ember value still renders on the row (it must, because it is editable there) — it just stops setting the glow. Rejected alternative: keeping both and pinning a rule that a Blazing current duty must still sit dim. That rule was already written into the spec as a ⚠️ warning telling future sessions not to "fix" it, which is a tell — a signal needing a permanent guard against its own obvious reading is the wrong signal. The Ledger inherits this for free: everything in it is finished, so nothing in it demands anything, and a uniformly flat Ledger is the honest render rather than a missing feature.
REPLACES: Supersedes the master-spec warning "TWO BRIGHTNESS SIGNALS COEXIST ON ONE ROW AND THIS WILL LOOK LIKE A BUG" (2026-07-30 late). That warning must be REMOVED at the spec regeneration, not softened.
STATUS: LOCKED

---

DECISION: Retire writes a new nullable column, quests.retired_at. It does NOT reuse archived.
DATE: 2026-07-30
WHY: Spec assumed Retire could reuse `archived` for free, since the column exists and isActiveQuest already filters on it. It cannot. `roll_missed_dailies` has been writing archived=true for stale daily instances since it shipped, so a Retired section filtered on `archived` would render every row the engine has ever swept as a duty a human deliberately retired. Two different meanings, one column. Rejected alternatives: a backfill distinguishing engine-archived from human-archived (requires judgment about historical rows nobody can reconstruct); a status enum value (touches the lifecycle machinery for a display concern). One nullable timestamptz, no backfill, historical residue stays invisible. It also records WHEN, which archived never did.
REPLACES: Supersedes the master-spec line "Costs nothing — archived already exists and isActiveQuest already filters on it."
STATUS: LOCKED

---

DECISION: Retire replaces Delete on any bounty that has ever been approved. Delete survives only on never-approved bounties.
DATE: 2026-07-30
WHY: Under roll-forward the two operations genuinely diverge. Delete ends the duty and erases it; Retire ends the duty and keeps it visible so nobody recreates it in six weeks — which is the entire reason the Slate exists. Before this, nothing user-facing wrote `archived` at all (only the rollover engine, server-side), so the spec's promised "Retired duties stay visible" section would have been decorative — a section that never fills. Deleting an approved bounty also destroys minted-ember history. Delete stays available on never-approved rows because there is nothing to preserve and without it a mistyped bounty is immortal.
REPLACES: Nothing — new decision. Completes the retired-duties behavior the 07-30 (late) Slate decision promised without a mechanism.
STATUS: LOCKED

---

DECISION: The Ledger is one list, role-filtered, read-only, sourced from quests and not activity_log.
DATE: 2026-07-30
WHY: The record-vs-scrapbook fork was FALSE and dissolved rather than being decided. The difference between an audit line and a trophy is who is reading, not what the data is — "Cade · Take out the trash · Jul 28 · +15" is both. Role-aware depth is one of the three disciplines: same data, different lens, filters never separate systems. So: one list, kid defaults to self, adult defaults to the hold. Sourced from `quests WHERE status='approved'` rather than activity_log, which is the intuitive choice and the wrong one — activity_log carries known verb drift, the member_admitted two-switch bug, and an unresolved question about what actor_label means. The quests table gives title, embers, member and timestamp clean. READ-ONLY is a deliberate capability REMOVAL from Quest Log, which allowed editing embers on an approved bounty: editing history on a surface that governs a currency is incoherent whether or not it retro-mints. Deferred, not decided: grouping beyond day headers.
REPLACES: Supersedes the master-spec line "ITS SHAPE IS NOT DECIDED" and the parking-lot OPEN DECISION "WHAT DOES THE LEDGER ACTUALLY SHOW?"
STATUS: LOCKED

---

DECISION: Neither the Slate nor the Ledger gets a tab. They replace Quest Log's existing Briefing card. The Hearth Log is deleted outright.
DATE: 2026-07-30
WHY: The nav question resolved cheaper than feared once the entry point was actually looked at: Quest Log lived in a Briefing card labeled ARCHIVE. The surface had already been demoted to a junk drawer by its own label. Tab bar stays at seven — one past mobile comfort and not worth making eight — with the Slate primary and the Ledger reached by a secondary link from it. "Does this already exist" is a creation-time question, not a daily-driver destination. Rejected: a symmetric segment control (jAIne's earlier lean), which implies equal weight between the two and would have lied about how often the Ledger gets opened. The Hearth Log is NOT inherited by the Ledger: it is raw activity_log rows behind a card reading VERIFICATION · TEMPORARY, a debug window shipped to production. Letting the Ledger absorb its job builds the admin/reporting surface, which north-star fences explicitly. Two cards became two cards by coincidence of position, not function.
REPLACES: Answers the master-spec navigation open question and the parking-lot OPEN DECISION "DOES THE SLATE REPLACE A TAB, OR ADD ONE?"
STATUS: LOCKED

---

DECISION: Under the All filter, One-offs render ABOVE Standing. Under a cadence filter, the One-offs section does not render at all.
DATE: 2026-07-30
WHY: Scott's call from the first live render. Under All, One-offs sat below fifteen standing rows — the transient section, the one that actually changes week to week, buried under the permanent one. Two rows on top costs nothing and Standing is still the first thing the eye lands on. The second half matters more: under Daily/Weekly/Monthly the section rendered an empty state reading "Filtered out by cadence," which is copy explaining why a section that shouldn't exist is empty. A one-off has no cadence, so under a cadence filter the section is INAPPLICABLE, not empty — no header, no counter, no empty state. Shipped free via Claude Code rather than Lovable, because it is frontend-only, bounded and eyeball-verifiable.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The Slate's empty state is the product's only lesson in recurrence.
DATE: 2026-07-30
WHY: A brand-new hold has no standing duties, so the "does this already exist" surface answers "nothing does" — which reads as a dead end unless it is doing another job. Nothing anywhere else in Emberhold teaches that a bounty can repeat. Pip's line names concrete duties (the trash, the dishes, Monday laundry) and states the mechanic plainly: it appears here once and stays. Paired with a create affordance that opens with recurrence already on, so the lesson ends in the thing it taught. Rejected: a generic "nothing here yet" empty state, which spends a real onboarding moment on nothing.
REPLACES: Answers the parking-lot OPEN DECISION "What is the SLATE's empty state?"
STATUS: LOCKED

---

DECISION: Two canon facts about the stranded past-due rows were wrong and are corrected here.
DATE: 2026-07-30
WHY: Canon recorded "Take out the trash" as SUBMITTED, due 2026-07-21, stranded. It is approved/archived with a future-dated successor at 2026-08-03; the Ledger renders its approval on Monday Jul 27. And there was ONE stranded past-due row, not two. This matters twice. First, the "submitted does NOT roll" guarantee — the only asymmetry in the roll-forward rule, the rule that protects a kid from an adult's lag — has ZERO live evidence, because no submitted row exists anywhere in the hold to test it against. It is built and unexercised, not verified. Second, the guilt-pile argument was built on an empirical base half the size canon claimed. The rule still stands on its own merits: unbounded accumulation across cycles is wrong whether or not it had happened yet. But it is recorded here that the conclusion outlived the evidence cited for it, and that the recon which produced both errors has not been audited.
REPLACES: Corrects the factual claims in the 2026-07-30 (late) roll-forward entry and in master-spec's recurring-lifecycle section. The RULE is unchanged; only the evidence behind it is.
STATUS: NOTED

---

DECISION: Claude Code draws zero Lovable credits — measured, not assumed.
DATE: 2026-07-30
WHY: Canon asserted this in three places and it was load-bearing for the entire lane-routing rule: the row primitive, the recon jobs, the grant probe and roughly half the parked work are all queued as "free, Code lane." Nobody had ever checked, and the two-way GitHub sync between the repos made it plausible that a Code push could draw down the meter on Lovable's re-sync. Scott measured the count before a full Code job (read, edit, build, commit, push, Lovable re-sync) and after. Zero change. Recorded as evidence rather than assumption so it is not re-litigated, and because if it had gone the other way, every "free" job in the parking lot would have needed re-scoping at once.
REPLACES: Nothing — converts a standing assumption into a verified fact.
STATUS: NOTED

—-
DECISION: "Bounty" is the universal object term. "Quest" is retired entirely.
DATE: 2026-07-30
WHY: The Quest/Bounty question had been LOCKED twice — 07-04 ("Quest is universal, Bounty
     survives only as a proper noun for the Briefing's Open Bounties strip") and 07-11
     (enforcement: Bounty banned from card-level UI). It kept resurfacing anyway, roughly
     monthly. THE DIAGNOSIS IS THE VALUABLE PART: the rule was not being violated, it was
     incoherent. It permitted BOTH WORDS ON SCREEN SIMULTANEOUSLY in different jobs — a rule
     a designer can hold in his head and a user structurally cannot. May sees two nouns for
     one object. So does a kid, so does a stranger, so does Scott every time he looks at the
     board. The repeated re-litigation was the correct response to an incoherent rule, not a
     failure of discipline. THE FIX WAS KILLING THE CARVE-OUT, NOT PICKING A WINNER.
     WHY BOUNTY WON, in order of strength: (1) TAXONOMY — Campaign > Quest is mush because
     both nouns imply narrative and neither says which is bigger; Campaign > Bounty is clean,
     the campaign is the arc and the bounties are the paid work inside it. (2) THE BOARD
     METAPHOR — bounties get posted on boards, and Emberhold has a board; quests get handed to
     you by an NPC. (3) THE VERB IS ALREADY OURS — you CLAIM a bounty, you ACCEPT a quest, and
     "claim" has been live in the product for weeks. (4) HONESTY — the ember economy is
     transactional; bounty says what the app actually is.
     THE 07-04 ARGUMENT THAT NO LONGER HOLDS: "Quest being universally understood is a FEATURE,
     the differentiation budget is already spent on ember/hold/hearth/Vault/Ranks." That was a
     real argument and it was defensible; Scott's counter is that the personality budget wants
     more, and the taxonomy argument above is independent of personality anyway.
     IDENTIFIERS DO NOT CHANGE. The `quests` table, the `bounty_posted` enum, every column
     name — all stay. Per the 07-11 identifiers-vs-copy rule this is a display-string sweep,
     not a migration.
     THE LANDING CHECK IS THE WHOLE RISK AND IT IS NAMED: the sweep rides on the Slate build
     prompt, never as a standalone job. A LOCKED rename with no landing is a wish — Feast→Hall
     sat unshipped for ten days after being LOCKED, and the 07-11 sweep was reported clean by
     Lovable and had OPEN BOUNTY stamped on six cards an hour later. REQUIRE THE GREP, file by
     file, with a justification for every survivor.
     KNOWN DEBT CREATED: `master-spec.md` lines 41-42 now contradict this entry and the spec's
     core object noun is wrong across 669 lines. The install-prompt description is its own
     LOCKED decision containing "quests" twice and needs its own supersede when the sweep runs.
     north-star's membrane line inherits the rename. All tracked in status and parking-lot.
REPLACES: SUPERSEDES "Quest is the universal object term. Bounty survives only as a proper
          noun" (2026-07-04) and moots its enforcement entry "Bounty is banned from card-level
          UI" (2026-07-11). Both stay visible per append-only; this entry governs.
STATUS: LOCKED

---

DECISION: The Slate and the Ledger replace Quest Log. The Slate holds anything with a FUTURE; the Ledger holds anything FINISHED.
DATE: 2026-07-30
WHY: May's QA item was "it's hard to see if a quest already exists for something weekly or
     monthly — high potential for duplication." THE MECHANISM: there is no object representing
     "the weekly trash duty." There are only instances, chained by approval — handle_quest_
     approval() mints the successor on the approval transition, so a recurrence exists only as
     a chain of rows each spawned by the last one's approval. Break the chain and the
     recurrence silently stops existing. To answer "does one already exist" you must inspect
     instances: the board hides future-dated successors (correct, LOCKED 07-21), and Quest Log
     applies no due_date filter so it is live instances, resting successors, stranded
     past-dues and completed history in one undifferentiated pile. NEITHER SURFACE ANSWERS THE
     QUESTION. Duplication was the correct thing to expect.
     THE SHAPE: two sections, because there are two object shapes and Quest Log's sin is
     treating them as one list. STANDING duties = one row per duty, forever, never leaving the
     Slate because there is always a next cycle; their individual completions go to the Ledger.
     ONE-OFFS = a single instance that lives on the Slate until approved, then moves to the
     Ledger permanently. Live/submitted/approved are STATES ON THE ROW for a duty, not separate
     rows; for a one-off, approved is what evicts it.
     RETIRED DUTIES STAY VISIBLE, dimmed and collapsed. This is not sentiment: if archiving a
     duty makes it vanish, someone re-creates it in six weeks and we are back at the
     duplication complaint that started this. The Slate has one job and "does this already
     exist" includes the ones you turned off. Costs nothing — `archived` already exists and
     isActiveQuest already filters on it. No new column, no migration.
     BRIGHTNESS IS THE STATE DIAL, REUSED NOT INVENTED. Bright = demands you now; dim =
     handled. Consequence worth having: A DIM SLATE IS A WELL-KEPT HOLD — the health read is
     visual before it is numeric, and the "X of Y current" counter is a derived nicety rather
     than the mechanism. WATCH FOR THIS LOOKING LIKE A BUG: two brightness signals coexist on
     one row, the ember value's Dim→Blazing tier ramp and the row's state heat. Not a conflict;
     the board already runs both on one card. But a Blazing 500-ember duty that is CURRENT must
     still sit dim, because the health read comes from the row and not the number.
     SCOTT SUPPLIED THE HALF THAT MATTERS AND jAIne DID NOT HAVE IT: there is no completion
     state for a cycle. Nothing anywhere says "the hold is well-kept this month." That is a
     missing habit loop sitting dead-centre of the thesis — out-habit, don't out-feature —
     and it was on no list.
     REJECTED — embers off the Slate on membrane grounds ("it becomes a second board"). This
     was jAIne's position and Scott overruled it with the better argument: jAIne was reasoning
     about READING, Scott about EDITING. Correcting a mis-valued bounty today costs a
     delete-and-recreate, which destroys the row's history and, for a recurring duty, breaks
     the succession chain. The Slate is the only surface where that correction is coherent.
     Embers render on the row and are editable there.
     REJECTED — a claim action on the Slate. Claiming happens on the board. The Slate reads and
     edits; it does not play. Keeps the membrane clean.
     DECOMPOSITION, per the north-star rule: this needs NO new object. `recurrence`,
     `recurrence_day`, `archived` and title are already on the rows. It is a grouping, a filter
     and a view. Content and structure, not architecture.
     DISSOLVED BY THIS ENTRY: "Quest Log applies no due_date filter — by design or by
     omission?" carried as an open decision since 07-26. It was never answerable as posed. The
     real question was what the surface is FOR, and the Slate answers it.
     STILL OPEN AND TRACKED IN PARKING-LOT: what the Ledger actually shows (record vs
     scrapbook), and whether Slate+Ledger replaces one tab or adds one against a nav already
     at seven.
REPLACES: Retires the Quest Log surface. Dissolves the 2026-07-26 open decision on Quest Log's
          missing due_date filter.
STATUS: LOCKED

---

DECISION: Unapproved recurring instances roll FORWARD as the same row. `open` and `claimed` roll; `submitted` does not.
DATE: 2026-07-30
WHY: Closes the design pass that 2026-07-26 opened and left explicitly unwritable ("a migration
     against an undecided rule is not writable"). Two instances are stranded on the live board —
     Grocery Shopping (due 07-06, claimed) and Take out the trash (due 07-21, submitted) —
     because roll_missed_dailies() filters on recurrence = 'daily' and nothing else in the
     system rolls anything.
     THE RULE: move the spawn trigger off approval. A weekly exists because it is the week, not
     because last week's got signed off. Extend the existing roll_missed_dailies() mechanism to
     all three cadences, ROLLING THE SAME ROW to the current anchor rather than spawning a
     successor. SAME ROW MOVING = NO GUILT PILE. NEW ROW SPAWNING = THE PILE. This is sweeping
     the class, and the shape is already shipped and proven for dailies.
     WHY `submitted` IS EXEMPT: a submitted bounty is sitting in the ADULT'S approval queue, not
     waiting on the kid. Rolling it forward moves a kid's completed work because an adult was
     slow. Take out the trash therefore stays put until someone approves it, which is correct
     pressure applied to the correct person. `claimed` rows keep their claim on roll, consistent
     with the 07-21 lifecycle decision that assignment is permanent and only the cycle resets.
     ROLL-FORWARD IS THE PRECONDITION FOR THE SLATE, NOT A COMPANION FIX. One row per duty only
     holds if the row moves. Spawn-on-approval without roll-forward gives three stranded trash
     rows by August and an unreadable Slate on day one. These ship together.
     SCOTT'S OBJECTION AND WHY IT WAS HALF RIGHT: he read the proposal as producing permanent
     board clutter — ten monthlies always sitting there with no way to tell what is left. The
     factual correction is that successor-spawning is ALREADY SHIPPED and LOCKED since 07-21;
     the successors exist the moment you approve and are merely hidden by isActiveQuest until
     their due date. So on August 1 every monthly lands on the board at once, today, with no
     change from us. This decision only alters the UNAPPROVED case, which currently strands
     forever. BUT THE OBJECTION WAS RIGHT ABOUT THE REAL GAP and produced the Slate.
     BOARD CLUTTER IS REAL, SEPARATE, AND CHEAP: a live monthly is real work someone should
     claim, so do not hide it — group it. Reuse the Lists collapsible sections (fb6aa99).
     Reuse over new systems.
     LIKELY SECOND PAYOFF, UNVERIFIED: the STALE chip predicate is probably `due_date < today`,
     which the stranded past-dues would fully explain. Verify before building anything separate.
REPLACES: Resolves "Unapproved weekly and monthly quests never roll forward" (2026-07-26,
          NOTED). Formally reverses the 2026-07-21 call that a weekly/monthly roll-forward job
          was redundant — that call was correct for approved quests and blind to unapproved ones.
STATUS: LOCKED

---

DECISION: Title truncation is ONE class — the title is the only element permitted to shrink. The fix is a shared row primitive.
DATE: 2026-07-30
WHY: Reported by May as a wall problem; Scott corrected it in the same breath — it is wherever
     bounty strings run long. Photos confirm it on Vault manage rows, the wall's Claimable list,
     and the wall's Recently Completed list. In every case the ember value, timestamp and action
     icons are protected from shrinking and the title absorbs the entire squeeze. Rows are
     vertically generous and horizontally starved: enormous Vault rows clipping a three-word
     title to fit.
     THE SUSPECTED MECHANISM, STATED AS A HYPOTHESIS BECAUSE THAT IS WHAT IT IS: on the Vault
     manage list, three rows carry the ADULTS ONLY audience badge and all three truncate; six
     rows lack it and none truncate. Three-for-three, six-for-six. That is a correlation
     observed IN A PHOTOGRAPH, not a finding read out of code, and jAIne initially asserted it
     as the latter. A read-only recon is briefed to DISPROVE it, along with three companions:
     that N independent row implementations exist with no shared primitive, that the title is
     always the flexible child, and that title length is not the variable.
     THE FIX: one shared row primitive — shrink-to-fit down to a legibility floor, then wrap,
     capped at two lines; badge drops below the title rather than beside it. Fix the mechanism,
     not the three instances. This is the isActiveQuest pattern wearing a different hat: N
     copies of one concept all making the same wrong call.
     REJECTED — MARQUEE / SCROLLING TEXT, which is what May actually asked for. The wall is a
     walk-past surface; scrolling text makes you stand and wait for the sentence to complete,
     which on a glanceable kiosk is worse than truncation because truncation at least fails
     instantly. The Claimable panel shows six rows — six simultaneously scrolling titles is a
     slot machine. Motion was requested because truncation felt broken; the fix is to stop
     truncating.
     REJECTED — A TITLE maxLength AT CREATION, AND IT WAS jAIne'S PROPOSAL. Killed inside one
     exchange by the photos: "Wash/Load Dishwasher" is 20 characters and "10 min massage" is 14.
     The names are not long; the containers are narrow. A cap would have made users pay for a
     layout defect. THE GENERAL TELL: when the "cheap upstream fix" constrains the user rather
     than the code, it is the wrong fix.
     LANE: Claude Code, not Lovable. Scott asked for it as its own Lovable push so it would be
     easy to revert; the real requirement was ISOLATED AND TRIVIALLY REVERTIBLE, which Code
     serves better — one commit, git revert, zero credits, and it is frontend-only so it is
     Code's lane under the one-writer rule anyway. READ THE REQUIREMENT, NOT THE NAMED TOOL.
     Auto-accept ON; bounded, reversible, eyeball-verifiable.
     ACCEPTED RISK, NAMED: a SHARED primitive touches Vault, wall and board at once — wider
     blast radius than a one-surface patch. Shared is still right, because three implementations
     making the same wrong call IS the finding, and the revert is still one commit. But Scott
     eyeballs three surfaces, not one. Do not stack this push with other changes.
REPLACES: Nothing — new decision.
STATUS: DRAFT — the badge-as-culprit mechanism is unverified. The named verification is the
        four-hypothesis read-only recon (H1 badge, H2 implementation count, H3 which element
        shrinks, H4 length as a variable). The DIRECTION is settled regardless of the outcome;
        what the recon can change is the shape of the primitive.

---

DECISION: DECLINED — the wall's mid-row clip at a scroll container's bottom edge is not a defect.
DATE: 2026-07-30
WHY: jAIne flagged that "Vacuum Upstairs" is cut through the middle of its row at the bottom of
     the Claimable panel, and argued that on an ambient display a half-row reads as broken
     rather than abbreviated — arguably worse than the ellipses. Scott declined it flatly from
     daily live use: he has looked at that wall every day and has never once read it as a bug.
     Recording it because an un-written rejected idea comes back every six weeks, and because a
     future instance looking at the same screenshot will flag the same thing.
     THE PROCESS NOTE IS THE REASON THIS ENTRY IS WORTH ITS SPACE: jAIne raised this unprompted
     while already holding two accepted findings from the same photos. That is finding-inflation
     — the instinct to return a full slate from a read rather than only what is there. The
     person who uses the surface daily outranks the agent reading a photograph of it, and there
     was no evidence offered beyond "it looks wrong to me," from an agent that cannot see.
REPLACES: Nothing — new decision.
STATUS: DECLINED

---

DECISION: User-authored content is out of scope for vocabulary audits. The string law governs app copy only.
DATE: 2026-07-30
WHY: jAIne flagged the reward "Video Game" as a Title Case violation sitting among sentence-case
     siblings, and filed it as string-law drift. Scott corrected it: a reward name is something
     a human in the household typed. It lives in their hold and on their board and it is not
     ours to normalise. "That's you letting humans be humans."
     THIS IS THE PRECISE INVERSE OF THE 2026-07-11 FINDING and that is why it needs writing down.
     That entry established that rendered strings derived from database identifiers ARE copy and
     must be audited as such — the test being "does it appear on screen," not "is it derived from
     code." Applied without this counterweight, that test sweeps up every reward name, quest
     title, list item and hold name a family has ever typed. THE COMPLETE TEST IS TWO-SIDED: does
     it appear on screen AND did the product author it. Both, or it is out of scope.
     A future vocabulary audit WILL flag this — the 07-11 rule is written down and this one was
     not until now. The Haiku sweep is already queued with three grep targets and would have
     "corrected" a family's typing on the way past.
     PRACTICAL BOUNDARY for the sweeps: app copy is anything shipped in the repo — labels,
     headers, Pip's lines, feed verb templates, empty states, onboarding text. Household content
     is anything a user typed into a form: bounty titles, reward names, list items, hold and
     member names, campaign names. Never rewrite the second category, in any sweep, for any
     reason, including the Bounty rename.
REPLACES: Nothing — this BOUNDS "Rendered strings derived from database identifiers are COPY,
          not identifiers" (2026-07-11), which stands unchanged within its scope.
STATUS: LOCKED

---
DECISION: Redemption approval records the PIN-verified adult via a validated optional parameter.
DATE: 2026-07-30
WHY: `approve_redemption` and `deny_redemption` now take `(_redemption_id uuid, _approver_id uuid DEFAULT NULL)`. NULL resolves to `auth.uid()`, which is correct on the Vault page where each co-parent is signed into their own account. Non-NULL is validated server-side for same-family AND active-adult, and RAISES on failure — never falls back to `auth.uid()`, because a silent fallback makes a rejected approver indistinguishable from an accepted one. The authority gate did not move; the acting session must still hold the parent role. The parameter refines ATTRIBUTION only. The defect was WALL-ONLY: the kiosk is the only surface where one session serves two adults, and `wall.tsx` was already computing the correct PIN-verified profile id and discarding it in the mutation's destructuring one hop before the RPC. Mechanical note that cost nothing only because it was caught in the brief: a defaulted parameter added via CREATE OR REPLACE creates a SECOND OVERLOAD, not a replacement, and every existing one-arg call then fails as ambiguous — the old signatures were dropped, recreated, and re-granted. REJECTED: fixing the `/first-run/adult/pin` string instead (ratifies the weaker behavior and has to be unfixed later); a mandatory parameter (breaks the Vault page); a silent fallback on validation failure. ⚠️ THE PRIORITY REASONING IN THE DOCS WAS WRONG AND IS RECORDED HERE SO IT ISN'T RE-DERIVED: `decided_by` has ZERO consumers — fetched by `select("*")`, rendered by nothing. It was carried as a breached-deadline 🔴 on a promise no user can falsify. It shipped because it cost ~1 credit and zero taps, and because leaving it open cost a re-argument every session. Verified on live data, not on glass — nothing renders the column.
REPLACES: The master-spec prescription that redemption should "move to match" quest approval. Supersedes the breached-deadline severity framing carried since 2026-07-29.
STATUS: LOCKED

---

DECISION: `profiles.role` is the authoritative role source for an arbitrary profile id. `has_role()` is valid only for the acting session.
DATE: 2026-07-30
WHY: There are TWO KINDS OF ADULT PROFILE and they are not interchangeable. A join-code adult has a matching `auth.users` row (`profiles.id = auth_user_id`). An adult minted by `create_adult_profile` has a `profiles.id` and NO auth user at all — they exist only as a sub-profile reached by profile-switch and PIN. `has_role()` reads `user_roles.user_id`, which only exists for auth-backed identities, so it returns FALSE for a legitimate, PIN-verified `create_adult_profile` adult — it would have rejected the exact co-parent the redemption fix exists to record. `profiles.role` + `profiles.status = 'active'` is authoritative for an arbitrary profile id and is trigger-protected by `enforce_profile_role_change` (parents only). `has_role(auth.uid(), 'parent')` remains correct for the acting session and only for it. This was surfaced by Lovable, not by jAIne — jAIne flagged profile-id-vs-user-id as a schema pedantry check in the build brief and it turned out to be a live false negative. It also sharpens the carried "two derivations of role" item: `FirstRunGate` (`profiles.role`) and `useMyProfile()` (`user_roles`) are not a style divergence — they genuinely disagree for one whole class of adult, and only one of them is correct.
REPLACES: Nothing — new decision. Sharpens the open "two derivations of role" item.
STATUS: LOCKED

---

DECISION: Quest approval is NOT the correctness model. `quests.approved_by` is the weaker path and the sweep target.
DATE: 2026-07-30
WHY: `master-spec.md` stated that quest approval's behavior was correct and redemption should move to match it. A recon briefed to DISPROVE killed it. `enforce_quest_update_authority` checks that the SESSION OWNER holds the parent role and that non-parents cannot change `approved_by`; `enforce_quest_family_refs` checks only that the written value names a profile in the same family. NEITHER checks that `approved_by` names an adult at all — a client can write a kid's profile id into it and both triggers pass, and nothing ties the value to the PIN-verified identity from that request. Redemption now validates strictly MORE than the thing we were told to copy. Tolerable rather than correct: intra-household under the walk-up trust boundary, no cross-tenant path, no consumer rendering it. Not scheduled — recorded so the next person reaching for a model reaches for `approve_redemption` instead. THE GENERAL LESSON: a prescription written in canon is still a hypothesis. Canon is the best current answer, not an exemption from testing, and briefing recon to disprove is what surfaces the difference.
REPLACES: The master-spec line "Quest approval's behavior is the correct one and redemption moves to match it."
STATUS: LOCKED

---

DECISION: NOTED — the discard-the-resolved-identity habit. Three confirmed sites, one prescribed shape.
DATE: 2026-07-30
WHY: Three places in this codebase where a caller correctly resolves an identity, hands it downstream, and the callee discards it for `auth.uid()`: (1) `wall.tsx` → `approve_redemption` — the `approverId` was destructured away one hop before the RPC, now FIXED; (2) `markFirstRunComplete` — contains a literal `void profileId;` and `mark_first_run_complete()` writes `WHERE id = auth.uid()`, still OPEN as critical path #1; (3) `actor_label` — the column does two jobs, sometimes "who clicked" and sometimes "who gets credit," still an open design call. These are NOT coupled: D5 was briefed to establish a shared dependency and FAILED. Redemption's identity comes from `verify_profile_pin` against the adults list; the marker's comes from `getActiveMemberIdSync()`. No shared helper, hook, or RPC — jAIne had put the coupling at better than even and was wrong. They share a HABIT, not a dependency, and the habit is a predictable consequence of the shared-session topology: `auth.uid()` is always available and always feels safe, so it wins by default whenever a function needs an actor. THE PRESCRIBED SHAPE, now with a shipped precedent in `approve_redemption`: a validated optional actor id, defaulting NULL to the session owner, checked server-side for same-family AND required role, raising on failure. Reuse it for the marker's `profile_id` and for `actor_label`'s `subject_profile_id`. Look for this habit wherever a client already knows who and a server function takes no actor parameter.
REPLACES: Nothing — new observation.
STATUS: NOTED
---

DECISION: The first-run completion marker ships as profiles.first_run_completed_at, written through a SECURITY DEFINER RPC scoped WHERE id = auth.uid(), with the backfill in the same migration as the column add.
DATE: 2026-07-29
WHY: The 07-27 decision established the marker as a per-profile database column and left one question open and scope-determining: whether a member can UPDATE their own profiles row under current RLS. That recon was deliberately SKIPPED to save credits, on the grounds that routing through a SECURITY DEFINER RPC works either way, adds no table grants on profiles, and matches the pattern already used three times (create_adult_profile, set_enabled_modules, set_household_timezone). Recon answered by not needing it. The answer turned up free anyway, in an old migration Scott pasted while looking for something else: CREATE POLICY "Update own profile" ON public.profiles FOR UPDATE TO authenticated USING (id = auth.uid()) — so a direct write WOULD have worked. THE RPC WAS STILL RIGHT, FOR A REASON NOBODY HAD AT THE TIME: that policy is also id = auth.uid(), which means direct-write carries the identical ceiling and no client-side change can ever reach a kid sub-profile's row. Only a SECURITY DEFINER function taking a validated profile_id can. The chosen path extends; the cheap path was a dead end. Shape as shipped: timestamptz NULL, backfilled to now() for every existing row in the SAME migration so no established household ever sees the flow; the function guarded AND first_run_completed_at IS NULL so it is idempotent and never rewrites a timestamp; search_path pinned; GRANT EXECUTE to authenticated then REVOKE FROM PUBLIC, in that order; zero table-level grants added on profiles. Fires on both exits from SetupShell — terminal handoff and Skip — because a skip that doesn't mark re-fires the flow forever, which is the hostage situation the one-directional rule exists to prevent. Non-blocking: navigation never waits on the write, since a failed write costs one extra showing and a blocked navigation costs the handoff. Verified end to end on prod: fresh creator and fresh join-by-code from a never-signed-in device.
REPLACES: Closes the open implementation question in the 2026-07-27 marker decision. Closes critical-path item #1 as carried in status.md.
STATUS: LOCKED

---

DECISION: The marker's READ and WRITE are both account-holder-scoped, they are wrong in the same direction, and they must be fixed as ONE change. Fixing either alone breaks the product.
DATE: 2026-07-29
WHY: FirstRunGate reads the account holder rather than the switched-into profile — carried since 07-27 as its own bundled item. mark_first_run_complete() writes WHERE id = auth.uid(). A kid sub-profile's row id is NOT any auth.uid() — profiles.id IS the auth user id, which is what current_family_id() has always relied on. So a kid completing the flow on a shared device updates ZERO ROWS and returns success: a silent no-op, not a wrong write, and nothing anywhere reports that the marker didn't land. THE TWO BUGS CURRENTLY CANCEL, AND THAT IS THE ENTIRE DANGER. The gate reads the adult's marker, which is set, so the kid flow stays dark and nothing misbehaves. Fix the read alone — which was the planned next bundle — and you get the loop: gate reads the kid's NULL, fires the flow, kid finishes, write no-ops, gate reads NULL again, fires again, every load, forever. jAIne had this queued as a standalone cleanup and would have shipped it. THE FIX is a validated profile_id parameter with a server-side family check — the same shape as the leading actor_label answer, which strengthens the case for that pattern generally: a validated subject id, never a substitution. THIS IS NOT THE isActiveQuest PATTERN AND CALLING IT THAT WOULD TEACH THE WRONG LESSON. isActiveQuest is two derivations of one fact drifting APART, and divergence announces itself the moment two surfaces disagree. This is ONE wrong assumption applied consistently to a read and a write, where the errors CANCEL and the system looks healthy. Divergence announces itself; cancellation hides. The general form worth carrying: wherever a read and a write share a scoping assumption, check them together, because a correct-looking system is not evidence that either is right. jAIne initially raised the opposite risk — that the RPC might fan out and mark every profile under one auth.uid() — which was structurally impossible. The real defect was the inverse and she found it only because Scott pasted the wrong migration file.
REPLACES: Supersedes the plan to fix FirstRunGate's profile resolution as a standalone item, as carried in status.md since 2026-07-27.
STATUS: LOCKED

---

DECISION: The first-run arrival gate must exempt EVERY walkthrough route tree, not only its own. A hold creator is a first-run-gate candidate like anyone else.
DATE: 2026-07-29
WHY: The gate was written for joiners and exempted pathname.startsWith("/first-run/") alone. Joiners never touch /onboarding/, so the creator case did not exist while the marker was stubbed to return true. The moment FIRST_RUN_MARKER_AVAILABLE flipped, it did: a brand-new creator is born with a NULL marker, the marker is role-agnostic, the gate fires on every mount of the authenticated shell, and /onboarding/add-family matches neither exemption — so the creator was yanked out of their own setup flow into the joiner tour and never completed setup. SHIPPED LIVE and caught on the first fresh-creator walk. Fixed by naming both prefixes literally; a general "is this a walkthrough route" abstraction was explicitly rejected as two literal prefixes being the whole fix. Verified four ways including the one the brief never named — /board with a NULL marker STILL redirects, which is the entire feature and had to survive the fix. THE PROCESS FAILURE IS jAIne'S AND IT IS SPECIFIC: her DO-NOT-BUILD list said "do not touch FirstRunGate or how it resolves which profile is active," meaning the profile-resolution bug. But the route exemption lives in that same file. She fenced the exact file containing the fix for the switch she was arming. Generalizes as: when a change makes a dormant file load-bearing, that file cannot be on the do-not-touch list. NOTED SEPARATELY, because it reads like a gap and is not: firrstRunEntryPath(role) distinguishes only parent from kid, with nothing separating creator from joiner. That is correct — a creator is marked complete by their own flow's terminal step or by skip, and never reaches the gate un-marked, so there is nothing for the gate to distinguish. RESIDUAL, ACCEPTED: a creator who bails mid-onboarding and returns to /board gets the joiner tour rather than resuming setup. Degraded, not broken, and strictly better than the pre-marker behavior, which was an empty board and nothing at all.
REPLACES: Nothing — new decision. Corrects the exemption written into the 2026-07-27 arrival-gate build.
STATUS: LOCKED

---

DECISION: The redemption-approval deadline is BREACHED. The arrival gate went live on 2026-07-29 with approve_redemption still recording the session owner instead of the PIN-verified adult.
DATE: 2026-07-29
WHY: The 07-27 decision set a deadline that was explicitly not arbitrary: the /first-run/adult/pin screen tells the user the PIN "keeps an approval or a redemption tied to the person who actually signed off on it," which is true for quests and false for redemptions, and the rule was that the string must become true BEFORE the arrival gate goes live — because until then the flow was dark and nobody had been promised anything. Flipping FIRST_RUN_MARKER_AVAILABLE IS that gate. It shipped anyway, on a deliberate call with 1.3 Lovable credits remaining: the breach is invisible in a single-adult hold, the fix is one prompt, and a live joiner flow was judged worth more than an exact promise. RECORDED BECAUSE THE CALL WAS MADE ON A SOFTER MEMORY OF THE STRING THAN THE STRING ACTUALLY IS. jAIne recommended publishing while recalling the copy as vague; the screenshots showed it names redemption explicitly. She corrected herself before the publish and Scott made the call with the real sentence in front of him, but the lean was formed on a misremembering, and a deadline that gets crossed on a deliberate call still has to be written down as crossed or it quietly becomes a deadline that never existed. Emberhold is now a live product making a specific claim it does not honor. First prompt of the next build window. DO NOT fix the string instead — that ratifies the weaker behavior by accident and has to be unfixed later, which the 07-27 entry already declined.
REPLACES: Amends the 2026-07-27 approved_by/decided_by decision by recording that its deadline condition failed. The decision itself is unchanged and still LOCKED.
STATUS: NOTED

---

DECISION: "Claude Code syncs to origin/main before reading" is not self-enforcing, and a stale clone is invisible until something else forces the comparison.
DATE: 2026-07-29
WHY: Code was briefed to fix the arrival-gate exemption. It read the file, made the edit, verified in-file, and only discovered at PUSH time that origin/main was 16 commits

——
DECISION: The PWA install tutorial ships without a service worker. A valid manifest + the iOS apple-mobile-web-app-* meta tags deliver home-screen install and standalone launch (presence); the service worker delivers offline resilience, a separate Gate B concern. The two were welded in status.md ("service worker = what makes installable PWA true") and are now unbundled.
DATE: 2026-07-29
WHY: Recon of the live head proved the app already had a valid manifest (name, start_url, display:standalone, 192+512 icons) and correct iOS tags (apple-mobile-web-app-capable=yes, apple-touch-icon, apple-mobile-web-app-title=Emberhold). Install already worked; nothing was blocking it. On iOS — most of the beta — there is no beforeinstallprompt and no automatic prompt ever, so a service worker does nothing for install there; the flow is Share → Add to Home Screen regardless. Building an SW "to enable the tutorial" would have delivered a benefit iPhone users never receive and delayed a shipped feature behind an unrelated one. The SW still matters (offline themed shell, a Gate B stranger-proofing item) and is deferred there, not cancelled — and it deserves its own careful pass, because a botched offline cache is exactly the silent-stale-content failure this project is paranoid about. Rejected alternative: build the SW first to unlock the Android automatic install banner — declined because that also needs the SW, benefits only Android, and both platforms are manual-gesture walkthroughs without it anyway.
REPLACES: Supersedes the status.md framing that treated the service worker as the prerequisite for "installable PWA." New decision.
STATUS: LOCKED

---

DECISION: The install tutorial is ONE screen appended as the terminal step of creator first-run — not a route, not a modal fired from elsewhere, not a multi-screen state machine. Its instructional asset is a real captured iOS Safari share-sheet screenshot framed in a charred "forge-window" ember border, not a painted illustration. It carries platform-conditional copy (iOS Share→Add to Home Screen; Android menu→Install app), a webview-escape line ("Not seeing Share? Open theemberhold.com in Safari first"), and a suppress-if-installed check (display-mode: standalone / navigator.standalone).
DATE: 2026-07-29
WHY: The first sketch was a nine-illustration, four-branch state machine with a confirmation screen and a persistent settings affordance — bloat wearing a thoroughness costume for a three-tap gesture. Right-sized to one screen riding the walkthrough that already exists (out-habit, don't out-feature, applied to onboarding itself). A real screenshot teaches a utility gesture better than an illustration — "your screen will look exactly like this, tap the highlighted row" is unambiguous — and it cost zero visual-lane work. The webview-escape line is not gold-plating: invite links open in Messages/Gmail in-app browsers where the Share→Add option does not exist, and that is the single most likely silent failure for a real cold install; it is one sentence and cutting it saves nothing. The forge-window border exists because a raw iOS share sheet is flat grey and would read as a foreign object dropped into an ember-dark flow (the "never flat or gray" floor). Deferred deliberately: a separate desktop branch, a confirmation/success screen, a persistent install affordance — deletable for beta.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Creator first-run is contextual setup, not a feature tour. The "Now show them" screen (the second four-beat loop recap, built as handoff.tsx) is cut. The "Welcome to Emberhold" overview (recap.tsx) and "The rest of your hold" surfaces-showcase (the-hold) are KEPT — even though the showcase is not strictly setup.
DATE: 2026-07-29
WHY: master-spec is explicit — first-run is contextual setup, not a feature tour, and the four-beat loop card is assigned to the landing page and on-demand help, not first-run. Two tour screens had drifted in. "Now show them" was the loop card restated a THIRD time in one sequence and had no defense — cut. "The rest of your hold" was kept against the strict letter of the rule on a deliberate call: it is the one moment that signals Emberhold is not only a chore game (Lists, Calendar, Campaigns live here too), which points directly at the thesis of owning the family-jobs Google does badly. That single breadth-signal earns its place; a third loop-recap does not. Future-jAIne: do NOT "finish the cleanup" by cutting "the rest of your hold" too — the deviation is intentional and reasoned, not an oversight. Mechanical note (in status): handoff carried the flow's terminal navigation (finish() → recap → the standalone check → add-to-home), so the cut required transplanting that wiring onto recap or the chain to the install screen would have been severed. Related: the first-quest doorway is now confirmed BUILT, which makes the master-spec "empty board / no path to first quest = top structural gap" line stale — a spec fold, not a decision.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: A first-party analytics tracker (flock.js, self-proxied via /~api/analytics) is present in the app <head>, most likely injected by Lovable. It must be named truthfully in the Gate C privacy policy.
DATE: 2026-07-29
WHY: Spotted during the head-tag recon. Cookieless-looking and gentle, but minors are genuine users (a join-by-code kid has a real auth row), so whatever it logs has to be disclosed when the privacy policy is written at Gate C. Recorded so a future session doesn't rediscover it cold while drafting the policy — know your surface before you write the policy. Not a this-session problem, and not a faces problem: there are no photos anywhere; avatars are illustrated.
REPLACES: Nothing — new decision.
STATUS: NOTED

---
DECISION: Forge v1 is a prescription engine with a log attached, not a logger with programming bolted on
DATE: 2026-07-28
WHY: Asked directly what they valued most about Fitbod, both Scott and May named "what exercise to do" and "what weight to use." Scott added the rest timer. Neither of them named logging. The log is the commodity — every free tier does it well and no one switches apps for it. This inverts the build order: "what weight" already exists in progression.ts (RPE autoregulation, plate-snapping, e1RM, ten tests), while "what exercise" does not exist anywhere and is the harder half. It was previously carried as a footnote ("no progression axis except load") and is now half of the single most-valued feature. The lucky consequence: if the whole value is "tell me what to do," then trust in the instruction IS the product — and the incumbent's most-cited weakness is that it won't explain itself. The reason string stops being a nice touch and becomes the moat, sitting directly on top of the #1 feature. Rejected: continuing to frame v1 as logging-first with programming as a later layer.
REPLACES: Supersedes the implicit logging-first framing carried since the module reframe. Sharpens "the bar is a USER TEST: Scott and May stop opening Fitbod" — the test is about prescription, not logging.
STATUS: LOCKED

---

DECISION: A pre-session gate accepts declared constraints before prescription is generated
DATE: 2026-07-28
WHY: May named the exact condition under which she overrides Fitbod: an injury that needs accounting for, or a bad night's sleep meaning there is no 100% available — "it always assumes 100% all the time." progression.ts autoregulates on RPE, which is a BACKWARD signal: it learns a session was too hard after it's been done and corrects next time, one session late, on exactly the day it was needed. Nothing in the current asset accepts a constraint before deciding. That is a real gap in the half we assumed was finished. The gate is one screen, a few taps, before generation. It is also where the reason string pays off hardest — "shoulder flagged, swapping overhead press for landmine press" fires at the precise moment she currently overrides. Rejected: inferring readiness from wearable data — there is no HealthKit web API, and a declared rough night is more honest than an inferred score because the user owns the result instead of arguing with it. Scope guardrail: this is one screen and three taps. The moment it grows a body map, a pain scale, or a history graph it has stopped being a prescription input and become a wellness tracker, which is a different product.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Readiness is a scalar; injury is a filter. Two controls, two lifespans, never one dial
DATE: 2026-07-28
WHY: May's complaint is two findings wearing one sentence. "I slept badly" means dial the whole session down — less load, maybe less volume, same movements — and it expires tonight. "My shoulder is hurt" means never prescribe this movement at any weight, and it is still true next Tuesday. One is a per-session multiplier; the other is a persistent exclusion. Jammed into a single "how are you feeling" dial, the result is wrong for both: either a tired user gets movements they shouldn't do, or an injured user gets their whole session watered down when only one pattern is the problem. Rejected: a unified wellness/readiness score.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: An injury flag carries its own resolution, asked at flag time; expiry rides the training split, not a timer
DATE: 2026-07-28
WHY: Two problems, both solved by asking rather than modeling. (1) Severity: at flag time the user picks "reduced load" or "avoid entirely." One extra tap deletes an entire severity-inference problem, and it keeps the call with the person who actually has the shoulder. Note the cost asymmetry — reduced load is nearly free because progression.ts already does load math, while avoid-entirely is the expensive branch, since avoiding creates a hole something must fill. That branch is the real build cost of the module and it is what makes the catalog non-optional. (2) Expiry, which is Scott's call and better than jAIne's proposal: flags don't decay, they get re-asked at the next session that touches the flagged region — "last push day you said your chest was hurt. Is it still injured?" The training split IS the timer. No decay logic, no duration declared up front, and the question fires at the only moment the answer is worth anything. jAIne had proposed either a duration at flag time or a nag after N skipped weeks; both invent a mechanism the product already has. The answer is three-way — still hurt / better / cleared — because "better" is the common real case and a binary forces a lie in one direction. "Better" moves avoid to reduced, reusing the fork rather than adding one. Filed as a downstream constraint, not a change to today's design: prescribing around a declared injury is a materially different act when the user isn't your wife. For the Drapers it's two adults making a call together; for a stranger it's an app that heard "my shoulder hurts" and told them what to lift. Liability posture, disclaimer surface, possibly a scope cut — the best feature in this session is the one most likely to need a lawyer before it meets strangers.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Core is programmed as real work, not accessory filler
DATE: 2026-07-28
WHY: May's second, separate complaint: Fitbod rarely or never makes core intense — it's always easy or filler. Unlike the readiness gap, this is not a modeling failure. It is a template bias: the incumbent files core as accessory and always will. Ours doesn't have to. This is a content decision inside the template library, costs nothing, and is a visible "we're not that app" signal in week one from a user who already articulated the complaint unprompted.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The exercise catalog is the single blocking dependency and it is the first build
DATE: 2026-07-28
WHY: Four separate features all require the same underlying tagging layer — movement pattern, muscle attribution, equipment requirement, and a substitution map. (1) Injury substitution: "swapping overhead press for landmine press" requires knowing what legitimately substitutes for what. (2) Rack-taken swaps: same lookup, different trigger. (3) Resequencing: knowing what else in this session is doable right now. (4) The pre-session gate's relevance check: "last push day you said your chest was hurt" requires knowing today's session touches chest. Four features, one dependency — which means the build order is no longer ambiguous and everything good in the module is downstream of it. Second in order and parallel, not blocked by the catalog: progression.ts has no progression axis except load. NextLoad returns {loadLb, reason, holdsAfter} — no reps, no sets, no tempo, not stubbed. It cannot express "175 instead of 185, so give me 8 instead of 5," which is now half the named differentiator. That is a Claude Code job — pure TypeScript, ten tests, zero Supabase imports, zero credits — and it should be scoped together with the pre-session gate's forward input path, since both are the same gap: the engine reacts and cannot yet be told anything in advance.
REPLACES: Nothing — new decision. Supersedes the status framing that treated the engine's missing progression axis as a carried footnote.
STATUS: LOCKED

---

DECISION: Make is an offline content factory for Forge, never a runtime
DATE: 2026-07-28
WHY: Scott already runs Make for the Etsy store and asked whether it could generate workouts on demand. Wrong seam, right tool. As a runtime it fails on latency (seconds with an LLM call in it), on per-operation quota that scales with every future user, and on constitutional rule 7's guardrail — if a feature could run deterministically and a model call was added anyway, that's the rule being gamed against itself. As a factory it is excellent and it buys the most expensive piece of the module: the catalog and a template library are a large pile of structured data that must exist before substitution or the pre-session gate work, they are generated once, and Scott reviews the output before it ships. This is a pattern the spec already uses — the experience layer generates vignette assets in the background, caches them, and selects instantly at the win. Same shape: generate offline, select locally, instant on the glass, zero marginal cost. Rejected: Make in the live prescription path.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Household scope changes Forge's economics, not its architecture; the LLM seam is session-start generation over an edge function
DATE: 2026-07-28
WHY: Bespoke for two people kills the free/paid split (no paywall for a household of two), per-user marginal cost (pennies), and quota pressure — all of which was product reasoning. Be generous with the model. What survives is structural. Generation happens at the start of a session, not per set: the model writes the plan, the client executes it. Once the session is materialized as a cached object, the in-gym loop is entirely local — sets, rest timer, load adjustment, logging. Three things hold regardless of scope. (1) The API key lives in a Supabase edge function, never the client — theemberhold.com is public and a key in the bundle is a key on the internet; this is the one place bespoke buys no shortcut. (2) The deterministic template path stays as the no-generation fallback — not extra work, it's the same engine doing double duty. (3) The reason string stays COMPUTED, never narrated — "avg RPE 6.7, adding 10 lb" is trustworthy precisely because it's derived; the model may pick exercises and structure, but it may not narrate math it didn't do. The real unlock of bespoke isn't the model, it's the context: two people's full training history, actual equipment, standing injuries, and individual response to volume, with no consent flow and no privacy surface. That is a prompt no product could ship. Building the architecture right now costs nothing and saves an unwind if this ever generalizes.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The client-side-engine rule survives on latency and sunk cost, not on connectivity
DATE: 2026-07-28
WHY: Scott's correction, and it was a correction of jAIne's method as much as her conclusion. master-spec.md Part II states the progression engine lives client-side "for one non-negotiable reason: a garage has unreliable connectivity." For a bespoke two-person build, connectivity is never a concern — WiFi or cellular, always. jAIne quoted the LOCKED rationale as an argument instead of testing whether it still applied. The rule survives on better grounds: the engine is already written and tested (deleting it costs a rewrite, keeping it costs nothing), and even on perfect LTE a rest timer and a plate calculation over the network is worse than one running locally — mid-set is the least forgiving moment in the app. Local isn't a hedge there, it's just faster. Two real unlocks follow from dropping the connectivity assumption: the exercise catalog can live in Supabase rather than shipping as a static asset, editable without a redeploy — which matters a lot while the substitution map is being tuned — and live mid-session regeneration becomes viable ("rack's taken, give me something else"), a genuinely good feature jAIne would have argued against an hour earlier on bad grounds. The connectivity clause is not dead, it is rescoped: it returns as a product constraint the day this meets strangers with basement racks and dead-zone commercial gyms. This is a Part II correction owed to the scheduled master-spec fold.
REPLACES: Supersedes the stated rationale of the client-engine rule in master-spec.md Part II (2026-07-25). The rule itself stands.
STATUS: LOCKED

---

DECISION: Contention resolution is a first-class Forge feature; the ladder is resequence, then substitute, then modify load
DATE: 2026-07-28
WHY: Scott and May train together on different splits and collide on shared equipment — one needs the Smith for bench while the other is squatting, one needs the bench rack while the other does pull-ups. This is the first thing in the module that no competitor could ship: it requires two people in one tenant sharing one equipment inventory, generated together. Fitbod structurally cannot do it because it has no concept of the person twelve feet away. Emberhold has it natively — constitutional rule 5 already has the module reading both members from the existing member table. The resolution ladder matters: the first move is resequencing within your own session, which costs zero workout integrity because it's still your workout reordered; substitute only when nothing else is doable; modify load last. Most collisions die at tier one for free. Deliberately NOT built: equipment presence detection or sensing who is on what — the users are ten feet apart and can see the rack; what's needed is one button that answers in under two seconds. Also rejected: minute-by-minute planning of both sessions, since real sessions drift and any timeline is wrong within ten minutes. Instead, bias at generation — both sessions plan from one call and the openers get staggered so nobody starts at the same rack. Near-zero cost, kills a chunk of collisions before anyone leaves the house. One new data field required: equipment must carry exclusive vs. shareable, since a Smith or squat rack is exclusive while dumbbells at different weights are parallel.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: A time budget is a generation input, not a schedule; duration estimates calibrate from the user's own rest-timer data
DATE: 2026-07-28
WHY: Scott values being able to tell Fitbod he has 45 minutes and have it plan accordingly — and notes it is often not close to accurate while still being useful. That reveals what the number's real job is: SIZING, not prediction. It shapes what gets generated (five exercises instead of eight); accuracy is a bonus. jAIne initially drew the line in the wrong place by lumping the budget in with minute-by-minute scheduling; Scott separated them correctly. The budget is an input, the schedule is an output, and only the output is worthless. We can also have the bonus that Fitbod can't: the rest timer is already being built, which means the app observes real set-to-set intervals including setup, plate changes, and conversation — per person, over months. Fitbod estimates from population averages; ours estimates from these two users. That is a byproduct of data already being collected, not a feature to build. It also extends the reason string past load into session shape: "45 minutes — keeping both compounds, dropping the second accessory." Shared-equipment time tax needs no model: if real sessions run 12% over, calibration absorbs 12% without anyone building a contention timing model.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Nag with the rest timer; never with session duration
DATE: 2026-07-28
WHY: Scott's call, and the principle generalizes past Forge: interrupt only when there is an action attached. The rest-timer alert has one — "go" is a thing you can do. A duration alert is a verdict you cannot act on, because you cannot lift faster; it turns a training tool into a stopwatch with anxiety. At most one soft check near the end ("20 minutes left, two exercises planned") as a prompt, never a verdict. Technical flag carried into the build: a rest timer that alerts is trivial while the app is awake and genuinely hard on a locked iPhone — iOS Safari suspends background JS timers and PWA push is fenced. Audio plus vibration with the screen awake covers the realistic case since the phone is already in hand between sets. This constraint is substantially defused by the always-on display decision.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Forge display mode is a separate always-on garage device that idles as the wall; the avatar is the session lane
DATE: 2026-07-28
WHY: Scott's design, arrived at in the shower, and it solves several problems at once. Device posture: this is NOT a toggle of the existing wall display mode — the spec already says the garage tablet is a different job, an input device at arm's length mid-rest rather than a display glanced at from across the room. The wall is glanceable and passive; a Forge screen is touched, wet-handed, between sets. Framing it as a toggle would get it built as a wall skin, which it isn't. But they reconcile cleanly through precedence rather than a new mode: idle defaults to the wall, a workout takes it over, with a manual "start workout" and "end session" and no presence detection — anything that tries to detect presence will be wrong at the exact moment someone is standing there sweating. A second screen in a daily-traffic room is also more surface for the habit to live on, which is the existing retention thesis. It also removes the iOS background-timer problem entirely — an always-on screen never suspends — which is a real technical argument, not just a nice-to-have. Layout: reuse the display mode's avatar-and-agenda structure. Tap an avatar, get the pre-session questions and Start; the lane expands into an agenda line carrying exercise, load, reps; tap the agenda item to log a set and the rest timer appears. Multiple lanes run this concurrently. THE AVATAR IS THE SESSION LANE — that is the answer to two people, two splits, and two timers on one screen, and it is not split view; it is the existing roster component doing double duty, which is exactly what constitutional rule 5 prescribes. A free feature falls out of it: with both agendas visible side by side you see a collision coming two lines ahead, so contention becomes avoidable on sight rather than resolved after the fact. One consequence recorded so it isn't re-litigated: the wall's never-mints-never-approves-never-edits rule does NOT bind a Forge screen. That rule protects the EMBER ECONOMY, and Forge has no economy — constitutional rule 2 is the entire reason it is a module and not a register. A Forge screen that writes sets operates outside the surface the rule protects. Superseded within this same session: the pre-session gate was first placed on a phone in the kitchen; it lives on the garage screen. One device, one place, and generation latency lands while the user is standing there anyway. Left open for Scott: whether the rest-timer alert fires from the screen or the pocket, and whether the timer shares the agenda cell with load/reps or replaces it — jAIne's objection to replacing it (rest is exactly when you want to see what you're about to lift) is flagged, not ratified.
REPLACES: Supersedes the same-session placement of the pre-session gate on a phone in the kitchen. Refines the parked note that "the garage wall is an input device, not a display" by adding the idle-defaults-to-wall precedence model.
STATUS: LOCKED

——
DECISION: The non-creator first run is the same SetupShell with a second step registry, and the flow writes nothing except a completion marker.
DATE: 2026-07-27
WHY: Recon question 4 — which of the five creator screens carry creator-only writes — came back ZERO. Neither setup-flow.ts nor SetupShell.tsx references role, creation, creator, or session state; the shell is neutral machinery, a step array and a chrome wrapper with a skip button. REJECTED: a genuinely separate artifact. Duplicating a container that carries no assumptions buys nothing. REJECTED, and this is the finding that actually decided it: rerouting joiners into the existing creator screens. Of the three screens that write, TWO are parent-gated — add-family (create_adult_profile checks has_role parent; the kid-insert path is gated by enforce_profile_insert_rules) and stock-vault (RLS policy "Parents create rewards"). Only first-quest is ungated. A kid walked into stock-vault eats an RLS denial behind a friendly mascot, which is precisely the failure the 07-26 curriculum decision exists to prevent, arriving through a different door. So the shell is reusable BECAUSE it is neutral and the screens are not reusable BECAUSE two of three throw. THE PROPERTY THAT MAKES ONE BUILD SERVE BOTH ROLES: a joiner sets nothing up — the hold already has a roster, a board and a Vault — so every step is teaching rather than doing, and a flow that writes nothing has no permission surface to differ on. That is a harder argument for role-aware depth than the parking lot had, which reasoned from the discipline ("same data, different lens"). There is nothing to build two of because there is nothing that writes. Adult array: board, approving, the Vault as operator, the PIN explainer, quest authoring. Kid array: board, claim/complete/submit, embers. Authoring is ABSENT from the kid array, not disabled and not gated.
REPLACES: Nothing — new decision. Resolves the "non-day-one first run" open decision carried in parking-lot since 2026-07-26.
STATUS: LOCKED

---

DECISION: The first-run trigger is a STATE check on arrival in the authenticated layout — not the create-vs-join fork in auth.tsx, and not an event on the pending→active transition.
DATE: 2026-07-27
WHY: status.md framed the gap as "auth.tsx routes create → the flow and join → /board directly," which reads like the fix belongs at the fork. It does not. REJECTED: hooking the fork. A joiner navigating from runSetup is still status='pending'; _authenticated/route.tsx renders PendingWaitingScreen instead of the requested route. Firing a walkthrough there teaches someone how to claim a quest and then parks them on a waiting screen for possibly a day — worse than teaching them nothing. REJECTED: hooking the pending→active transition as an event. PendingWaitingScreen polls every 8s and calls router.invalidate() when it clears, which is a real seam — but admission frequently happens while the joiner has the app closed, and they return the next day to a plain /board load with the event long gone. An event-based hook silently misses that path, which is probably the majority path. THEREFORE it must be a state check performed on every mount of the authenticated shell, which is what makes a persisted completion marker mandatory rather than convenient. Placement: immediately after the existing pending check in RouteComponent.
REPLACES: Nothing — new decision. Corrects the framing of critical-path item 3 as carried in status.md.
STATUS: LOCKED

---

DECISION: Both non-creator doors are in scope — join-by-code and profile-switch. A minor with their own phone and a join code is normal traffic, not an edge case.
DATE: 2026-07-27
WHY: jAIne framed the kid joiner as a rare bird on the grounds that master-spec says kids have no auth identity — they are sub-profiles under an adult's session, reached by tapping a face. Scott falsified it in one sentence: a 13-year-old with their own phone comes through the front door with a join code, and the join form has an adult/kid selector they will use. That is normal traffic for the top half of the kid range. So there are two real doors with real volume, not one door plus an edge case, and the completion marker must serve both. THE SELECTOR CANNOT CORRUPT THE FLOW, BY CONSTRUCTION: complete_household_setup writes role='kid' and status='pending' literally and puts the selection in requested_role, advisory only; the confirmed role does not exist until a parent runs admit_pending_member. The flow fires strictly after that, so it never reads the advisory value — no guard needed. NOTED, AND IT SHARPENS AN EXISTING GATE C ITEM: master-spec claims kids-have-no-auth-identity as the COPPA advantage — no kid email, no direct collection. That is FALSE for this door. A minor joining by code has an email, a password, and an auth row. At 13 it is fine and at 10 it is not, and nothing in the path asks. COPPA is already named in Gate C so this is not new work; what is new is that the shield the spec leans on does not cover the door strangers will actually use.
REPLACES: Nothing — new decision. Corrects jAIne's own framing within the same session.
STATUS: LOCKED

---

DECISION: The first-run completion marker is a per-profile database column. Not a derived gate, not client storage.
DATE: 2026-07-27
WHY: Recon confirmed no such field exists — nothing on profiles, nothing in any migration, no RPC. REJECTED: a derived gate, and the rejection is the interesting part. FirstQuestDoorway already solves an activation question with no flag at all — a live unfiltered count of the hold's quests, with a comment explaining the choice. Genuinely good pattern and it does not transfer. It works there because the gate and the goal are the same fact: the doorway asks you to post a quest, one exists, done. First run teaches four things and none of them is a queryable fact. And the killer: A DERIVED GATE CANNOT REMEMBER A SKIP. Skip navigates to /board, /board re-evaluates, the flow re-fires forever — inverting the 07-26 one-directional decision and turning a guided first run into the hostage situation that decision exists to prevent. The doorway escapes this because it is a dismissible banner with a sessionStorage soft-suppressor; a full-screen takeover cannot. REJECTED: localStorage or sessionStorage. On a shared device one profile's marker suppresses every profile's flow — the Vault-favorites persistence debt repeating in a place where it breaks the feature outright. IMPLEMENTATION SEAM: one read function and one write function in src/lib/first-run.ts, every consumer routed through them, stub bodies behind a TODO until the column lands. THE TRIPWIRE, AND IT IS NOT OPTIONAL: FIRST_RUN_MARKER_AVAILABLE and both function bodies must go live in a single change. Flip the constant while hasCompletedFirstRun still returns a literal true and the route guard goes live against every walkthrough route, which is exactly the regression this session created and caught. That belongs in the build brief, not in a code comment. OPEN AND SCOPE-DETERMINING: whether a member can UPDATE their own profiles row under current RLS. If not, this is a column plus an RPC.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The joiner flow EXPLAINS the adult PIN and never collects it.
DATE: 2026-07-27
WHY: A fail-closed intercept already forces PIN creation at profile switch-in for any PIN-less adult, and the 07-26 join-path fix established that a joining member's PIN is set after an existing parent admits them. Collecting in the flow duplicates a mechanism that already exists and works, and it would be the only write in a flow whose entire structural property is having none — the property that lets one build serve both roles. REJECTED: collecting it "while we have their attention." That reasoning would hold if nothing else owned the job; something does. NOTED: the shipped screen reads well — it states what the PIN protects, tells the user they will be asked for one the first time it is actually needed, and explicitly says there is nothing to do here, which is the note that stops someone hunting for a field that is not there.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: DECLINED — "Replay the intro" does not fold into the first-run flow. The PipHelp sheet stays exactly as it is and nothing points at the walkthrough routes.
DATE: 2026-07-27
WHY: jAIne proposed the fold, argued it as one artifact with three consumers, and then killed it on Scott's prompting. THE SEAM IS MODALITY. The first-run flow is a full-screen routed takeover with SetupShell chrome and a skip button — correct for a first run, where taking over the screen is the point. Replay is a bottom sheet reached from a help affordance and dismissed. Folding means either replay becomes a full-screen takeover, far too heavy for "remind me how approval works," or the first run renders in a sheet, which is wrong for someone's first thirty seconds. Three smaller confirmations point the same way: the audiences have opposite knowledge states (never-used versus three-weeks-in looking up one thing), the exits differ (hand off to the board versus return me to where I was), and the existing sheet works today. THE CONFLATION WAS CONTENT WITH CONTAINER. The four beats are shareable, and if they drift apart later that is when a shared source gets extracted — not before, because extracting a shared module for two consumers when one is four hardcoded blocks that already work is premature. AND THE OPEN ITEM WAS NEVER THIS: the carried concern was that an established hold can reach /onboarding/first-quest by URL and be walked through cold-start copy. That is a routing guard, and the completion marker closes it regardless of what replay does. The two sat in one parking-lot bullet because they share a question — what does a non-day-one user see — not because they share an artifact. Correct bundling for the workshop, wrong for the build.
REPLACES: Closes the "replay half" of the non-day-one first run open decision. Options (b) read-only mode and (c) re-run the real flow are both declined with it.
STATUS: DECLINED

---

DECISION: DECLINED — the app does not tell a joiner what role they were confirmed as. requested_role is not preserved to enable it.
DATE: 2026-07-27
WHY: The case is real and unpleasant: a 13-year-old picks "adult" on the join form, a parent admits them as a kid, and nothing acknowledges it. Recon established there is no surface for it and no data to build one from — admit_pending_member sets requested_role = NULL in the same UPDATE that sets the confirmed role, so the evidence is destroyed at the moment of admission; activity_log has no role column and could not carry it; the "Admitted to the hold" toast fires on the admitting parent's own client and cannot reach the joiner's device. REJECTED: preserving requested_role to power a notification. Rebuilding destroyed evidence to deliver bad news is the wrong fix twice over. An app telling a teenager "you were downgraded" lands worse than a parent telling them "kid for now, we'll revisit," and it delivers that message as a first impression to exactly the user whose day-8 retention the whole flow is built for. THE DEMOTION IS A HOUSEHOLD CONVERSATION, NOT A SYSTEM MESSAGE. What the app does owe: the parent should see what was requested at the admit moment, and the kid flow must never frame role as status. The second half shipped — no /first-run/* screen says "you're a Kid" or compares roles, which also means the parked "retire identity-first role labels" sweep was satisfied on net-new strings at zero cost.
REPLACES: Nothing — new decision.
STATUS: DECLINED

---

DECISION: approved_by's behavior is correct and decided_by moves to match it. Quest approval records the PIN-verified adult; redemption approval must do the same.
DATE: 2026-07-27
WHY: The fork has sat open since 07-27 early as "pick one, make them agree," with quest approval framed as better-for-a-shared-wall (it credits whoever actually authorized) and redemption framed as safer (it cannot be spoofed). It was resolved by a piece of copy written by someone who did not know the fork existed. The shipped /first-run/adult/pin screen tells the user the PIN "keeps an approval or a redemption tied to the person who actually signed off on it." That is true for quests — wall.tsx writes approved_by: adultId — and false for redemptions, where approve_redemption hardcodes decided_by = auth.uid(), the wall's session owner, and its caller accepts an approverId it never sends. So Adult B approving a redemption on Adult A's session records A. THE COPY IS THE PROMISE THE PRODUCT WANTS TO MAKE, and it was arrived at independently by someone reasoning about what the PIN is for. That is decent evidence about which behavior is right. REJECTED: fixing the string. It would ratify the weaker behavior by accident and have to be unfixed later. DEADLINE, AND IT IS NOT ARBITRARY: the string is already live but the flow is dark, so nobody has been promised anything yet. The string must be true before the arrival gate goes live — which makes this the same change as the completion-marker column, not a separate item. NOTED: enforce_quest_update_authority gates on has_role parent but never compares NEW.approved_by to auth.uid(), so any parent session can stamp any adult. Under the walk-up trust boundary that is not a forgery risk; it is the mechanism that makes the shared-wall behavior possible in the first place.
REPLACES: Resolves the 2026-07-27 (early) open decision "approved_by vs decided_by — which one is right?"
STATUS: LOCKED

---

DECISION: Quest creation is discoverable from a kid's board through the global FAB, and campaign.$id gates it while the FAB does not. The curriculum decision stands; the inconsistency is logged.
DATE: 2026-07-27
WHY: Carried since 07-26 as a one-line recon nobody had run — "is quest creation DISCOVERABLE from a kid's board, or merely reachable?" Briefed as a hypothesis to disprove and partially disproved. BottomBar.tsx's FAB opens a menu containing "New quest" with no role, isParent, or useActiveMember reference anywhere in the file; QuickAddTray takes no role prop and renders every chip unconditionally; board.tsx's kid branch renders /create chips gated on data state, not role. So a kid does see a create affordance, plainly. BUT campaign.$id.tsx wraps its "New tagged quest" link and "Tag an existing quest" button in {isParent && (...)}, so a kid viewing a campaign sees neither. THIS CHANGES NOTHING ABOUT THE PERMISSION: approval is the real gate, a kid can propose a 500-ember quest and cannot cash it, and onboarding simply never teaches authoring to a non-approver. The kid first-run array omits it, which is the decision working. WHAT IT DOES CHANGE is that two surfaces now disagree about one rule — the same class as approved_by versus decided_by. Removing the campaign gate is consistent with the curriculum decision; adding a FAB gate contradicts it. Recorded as NOTED rather than decided because the answer is cheap and neither surface is currently doing harm.
REPLACES: Nothing — new decision. Closes the open recon line carried from 2026-07-26.
STATUS: NOTED

---

DECISION: A guard that protects a route can break the flow that uses it, and "inert" is not one behavior.
DATE: 2026-07-27
WHY: Recorded as a build-model lesson because it produced a live regression on working code inside the session that shipped it. Two instructions, each individually correct: reads stub to true so nothing fires for existing members, and the same predicate guards the walkthrough routes so an established hold cannot URL-walk into cold-start copy. Jointly, redirectIfFirstRunDone resolved a profileId for every authenticated user and unconditionally redirected — and because that guard was also applied to the five creator /onboarding/* routes, runSetup's navigate to /onboarding/add-family bounced straight to /board. THE 07-26 ACTIVATION FLOW WAS BYPASSED FOR EVERY NEW HOLD: empty board, no first quest, no reward, no handoff. The highest-value thing shipped in the prior week, undone by a guard intended to protect it. Caught in dev, fixed at 369164e, never published broken. THE ROOT CAUSE IS SEMANTIC, NOT MECHANICAL: one predicate served two consumers that need OPPOSITE null behavior. For an arrival gate, inert means don't fire. For a route guard, inert means don't block — not always block. The default that is safe for one is maximally unsafe for the other. Fix: FIRST_RUN_MARKER_AVAILABLE short-circuits the guard before any auth call, and it has the useful side effect of making /first-run/* reachable by URL, which is what finally allowed the screens to be walked on the glass. THE STANDING RULE: before adding a guard to a route, name every consumer of that route and state what each one does when the predicate is unavailable. TWO SECONDARY LESSONS FROM THE SAME EPISODE. jAIne proposed flipping the read stub to false in production to see the screens — a change that would have synced to Lovable and handed every member of all thirteen households an unskippable adult walkthrough on their next board load, unskippable because the write no-ops — and described it as "one line, reversible" without following it downstream. And jAIne twice asserted the screens were unverifiable when the copy was plain text the whole time, filing the checkable half under "can't check."
REPLACES: Nothing — new decision.
STATUS: NOTED


---

DECISION: The Forge is scoped to the Draper household, not to strangers. It stays merged inside Emberhold.
DATE: 2026-07-27
WHY: Emberhold has a thin market and real teeth — a family that finds it cold could plausibly say "hell yeah." The fitness market is the opposite: saturated with competent products, so the bar for a stranger-facing Forge is far higher than Emberhold's and the ceiling is lower. Scott's own framing: don't dilute the "wow" of Emberhold with a "meh" Forge. REJECTED: rebuilding Forge as a standalone tool for two users. That reverses a decision made on grounds audience does not touch. The 07-25 merge was decided on infrastructure — no second auth, no second tenant bootstrap, one member table, one PIN system, one avatar pipeline, one unlock — and every one of those costs stays fixed while the audience shrinks to two. Standalone-for-two is a WORSE proportion than standalone-for-strangers, and proportion is exactly what killed the 07-25 prototype (a tool wall and a metal-composition system, still unable to log a working set). REJECTED: the fear that Forge dilutes Emberhold. It is structurally impossible — enabled_modules plus a gated route with no nav entry means a stranger household never renders it. The switch already exists and needs no build. The real risk is SUBSTITUTION, not dilution: a Forge session happening instead of a Gate B item rather than alongside one. That is the tripwire. Money survives intact: ~$192/yr of Fitbod cost avoidance counts identically to $192 of revenue against a $636/yr tooling-cost target.
REPLACES: Nothing — new decision. Constrains the 2026-07-25 module-merge decision by scoping its audience.
STATUS: LOCKED

---

DECISION: "A full Fitbod replacement" is retired as Forge's standard. The bar is a user test: Scott and May stop opening Fitbod.
DATE: 2026-07-27
WHY: Read literally, "full Fitbod replacement" has no exit condition — Fitbod has a funded team and an edge-case surface Forge will never match. That standard makes A permanently unshippable, which is the opposite of what the 07-26 call intended. Read as a user test it is achievable and probably closer than it looks, because Forge serves two known users in one known gym with no edge cases. Consequence: constitutional rule 7's free/paid split no longer applies to Forge. Nobody buys an LLM programming tier in a two-person app, and LLM programming was the sole paid feature.
REPLACES: The 2026-07-26 call that "A must be a FULL Fitbod replacement, not a thin MVP." That entry is SUPERSEDED 2026-07-27.
STATUS: LOCKED

---

DECISION: Forge stores weight in pounds and logs dumbbell load per hand. Exercises carry a bilateral flag.
DATE: 2026-07-27
WHY: Most American gyms mark plates and dumbbells in lb, and progression.ts already names every field _lb. Kg is not a display toggle on lb data — a standard bar is 20kg (44.09lb, not 45) and standard kg plates don't map onto lb denominations, so the achievable load set genuinely differs. Store one unit, convert at the glass. Per-hand matches Fitbod (so numbers stay portable) and avoids mentally halving at the rack every set. REJECTED: logging total. It reads cleaner in tonnage math and is worse everywhere a human touches it. Per-hand forces a bilateral boolean on the exercise row — a 40lb bilateral press and a 40lb single-arm row are the same logged number and twice the work apart. That flag cannot be added after data exists without every historical volume figure silently changing. Also noted: a dumbbell e1RM must never be estimated from a barbell e1RM or vice versa; 40 per hand is 80lb moved but is not an 80lb bench.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Forge equipment is standards-plus-override. Users select equipment TYPES; presets generate the load set. No plate-by-plate inventory entry.
DATE: 2026-07-27
WHY: Scott's call, overruling three messages of jAIne building an inventory system with Scott's gym as the schema rather than as a row in it — the same failure mode as the standalone app, solving for the case in front of it and calling it architecture. A real user will not enter every plate they own. REJECTED: dropping inventory entirely. progression.ts needs to know what loads exist or plate math is guesswork; the inventory is not the mistake, making a user type it in is. Presets write standard inventories behind a type selection. THE TEST: if Scott's gym cannot be expressed as preset + overrides, the preset model is wrong — his rack is unusual enough to be a genuine stress case. Corollary, also Scott's call: when the prescribed load isn't achievable, the user adjusts manually and the app compensates in reps rather than the system modelling finite plate counts. That compensation is the differentiator — it's the reason string running in reverse, and it's where Fitbod goes silent. It needs a validity floor: below some deviation the app must say "that's a different exercise now" rather than compute an equivalence it doesn't have.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Forge cardio is included, self-reported, and prescribed rather than autoregulated. Sessions hold entries of two shapes and may be mixed.
DATE: 2026-07-27
WHY: jAIne conflated "a PWA cannot VALIDATE cardio" with "cardio cannot be INCLUDED." Time and resistance are self-reported exactly like every other number in the app; the absence of HealthKit only removes validation, not participation. Cardio entries carry duration and optional resistance, nothing computes them, and there is no reason string because there is no reasoning — that is honest and sufficient. Scott's own example forced the mixed-session requirement: "10 minute bike warmup" is the front of a lift day, not its own session, and if cardio only exists as a separate session type the warmup gets skipped or creates junk data. Two entry shapes must exist from day one; retrofitting the second after the first has data is the expensive version. NOTED: cardio and bands are the same category — equipment producing no load set. Bands stay out of v1, but once a non-load entry shape exists they stop being a second engine and become a rank plus reps. Door unwelded, not opened. DECLINED for now: a session-RPE field for cardio progression — do not store a column until something reads it.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The activity-log actor-forgery item is downgraded and re-scoped from a security fix to a design question about what actor_label means.
DATE: 2026-07-27
WHY: Read-only Code sweep, briefed to disprove, killed jAIne's own recommendation. activity_log.actor_id is already derived server-side from auth.uid() and cannot be faked from the client; family_id derives from current_family_id(); _ember_delta is already nulled for non-parents. Only actor_label — the rendered display string — is client-supplied. So the exposure is a feed that can render a wrong NAME over a row that still records the truth: cosmetic-layer lie, auditable data beneath. Under the walk-up trust boundary this was never a cross-tenant concern. REJECTED: deriving actor_label from auth.uid(). It is a regression, not a fix. Four of six logActivity call sites deliberately pass someone other than the caller — Briefing.tsx and quest.$id.tsx pass claimed_by ?? assigned_to, vault.tsx's approve path passes r.requested_by — because a parent approving a kid's quest must show the KID's name. Deriving from the session would rewrite every completed-quest feed entry to the approving parent. The column is doing two jobs: sometimes "who clicked," sometimes "who gets credit." The likely shape is a validated subject_profile_id with the label derived server-side from it — a third field, not a substitution. That needs Scott. The sweep also confirmed actor_label is the only client-supplied identity field in the entire migration set, and that admit_pending_member / deny_pending_member both stamp from auth.uid(). LESSON: severity in a doc outlives the evidence for it. This was carried three sessions as "top open security item" on a summary nobody re-derived.
REPLACES: The 2026-07-26 entry ranking activity-log actor forgery as the top open security item and prescribing derivation from auth.uid(). That entry is SUPERSEDED 2026-07-27.
STATUS: NOTED

---

DECISION: families.timezone gets a set-once silent heal plus an explicit parent-triggered reset button. The household clock never follows a device automatically after it has been confirmed once.
DATE: 2026-07-27
WHY: The thirteen existing holds were BACKFILLED with the America/Los_Angeles default when the NOT NULL column landed 07-23; timezone detection did not ship until 07-26, so it never ran for any of them. At least one hold (Phaeaz) is known to be outside Pacific. Wrong zone means dailies rolling over mid-evening — a silent correctness bug in the engine's core loop with no user-facing lever. REJECTED: wiping the column to let it repopulate. Detection is a browser API that runs on the user's device when they are present; Postgres cannot go get it. Wiping produces thirteen NULLs and breaks rollover for holds that are currently correct. THE GENERAL RULE: client-detected values only arrive when a client shows up. REJECTED: a UTC offset instead of an IANA name — offsets aren't stable (LA is −8 in winter, −7 in summer) and the failure wouldn't look like a timezone bug, it would look like dailies rolling at 11pm twice a year. REJECTED: NTP (pool.ntp.org, time.nist.gov) — it answers "what instant is it," which nothing here is confused about. The question is "which calendar day is it for this household," and the off-the-shelf answer is the IANA tz database, which Postgres ships and household_today() already uses. REJECTED: silent auto-correct on any parent login — Scott overruled and was right. A parent travelling for work would move the whole household's clock for a week; his family's dailies would expire mid-evening and nobody at home would connect the two. A household's timezone is where the household LIVES, and a traveller's device is exactly the wrong source of truth. REJECTED: per-member timezones — contradicts the 07-23 call that the household's day is what matters, and it should, because a chore is done at a house, not at a person. REJECTED: a settings form alone — it builds a lever with nobody's hand on it; someone still has to know Phaeaz's real zone and go set it. The heal fixes the thirteen with no interviews; the button covers the once-a-decade relocation. Moving is a decision and travel is a trip, and no signal from a device can distinguish them — so the system stops guessing and a human confirms. Implementation: families.timezone_confirmed_at timestamptz NULL, a parent-gated SECURITY DEFINER set_household_timezone() validating against pg_timezone_names, silent heal only when confirmed_at IS NULL, and NO table-level UPDATE grant on families (the 07-15 privilege-escalation fix stays intact — families holds is_founder).
REPLACES: Nothing — new decision. Closes the 2026-07-26 open finding that families.timezone has no update path.
STATUS: DRAFT — contingent on the build landing and on verifying, on the glass, that a second sign-in changes nothing.

---

DECISION: Session lane is declared at open. Four values: design-only, design + Lovable, design + Code, full.
DATE: 2026-07-27
WHY: Scott carries conversations across a day — a thread can start in the gym between sets and finish at a keyboard. jAIne briefing into a lane that isn't open wastes the session and makes good design work feel unfinished: build prompts against an empty credit balance, Code recon at someone standing at a rack. REJECTED: inferring the lane from location. Falsified inside one conversation — Scott was at a desktop with twelve credits left, so "at a keyboard" did not mean "can build." Credits and hands are INDEPENDENT switches. REJECTED: treating the gym as design-only by definition. Lovable is a browser tab and runs fine from a phone; only Claude Code is genuinely desktop-bound, because reviewing what an agent did to a codebase on a phone is a bad idea. Design is always on. NOTED: rest-period design is a real and productive mode — a large share of Emberhold was built that way, and this session's best corrections (cardio, standards-over-Scott's-gym, rep compensation, per-hand) came from it. jAIne asserted this mode was leaking out of the repo; it was not. Scott commits the outputs and the capture muscle is working. Folds into playbooks/session-protocol.md on a session where jAIne can read the current version first.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Fitness Option A is a FULL Fitbod replacement, not a thin MVP with the game deferred. `/setup/intent` is parked until Forge is built.
DATE: 2026-07-26
WHY: The A-versus-B framing risked being read as "ship a minimal logger now, add the interesting part later," which is the wrong shape twice over. FIRST, IT MISREADS WHAT A IS FOR: A's job is to cancel a $15.99/mo subscription, and a subscription does not get cancelled by a tool that does most of what the paid one does. If a lifter still opens Fitbod for anything — program authoring, e1RM trends, plate math, rest timing — then A has not shipped and the $192/yr is not recovered. Partial replacement recovers zero dollars while consuming the whole build. SECOND, IT INVERTS THE RISK THAT ALREADY MATERIALIZED ONCE: on 07-25 a fitness prototype grew a tool wall and a metal system while still being unable to log a working set. The standing risk is that the game is the most interesting part to build and the tool is the part that pays. Declaring A "full-featured" is the guardrail — it means the fun cannot start until the tool is genuinely done, rather than the tool being declared done early so the fun can start. THE FUNCTIONAL BAR, from the market read: offline-first logging; set entry that survives a sweaty one-handed rest period; RPE/RIR on every set; e1RM trend and weekly volume; rest timing; template-driven program and block authoring. The differentiator remains explainability, not AI — deterministic autoregulation off RPE surfacing a one-sentence reason. B is deferred, not rejected, and does not get designed until A has been used for a month. RELATED PARKING CALL: /setup/intent has been carried as an open question for three sessions. It is unrouted, unlinked, never loaded by a human, and nothing reads enabled_modules — because module intent has nothing to point at until a module exists. It is therefore not an open decision at all; it is a dependency of the fitness build. Parked with a trigger: finalized when Forge is built, not before. Do not re-litigate it in the meantime, and do not delete it either — deleting and rebuilding costs more than leaving it.
REPLACES: Sharpens the 2026-07-25 "A ships before B is designed" decision, which set the order but not the bar. Closes the "/setup/intent's fate" open decision by converting it to a parked dependency.
STATUS: LOCKED
---

DECISION: The table-grant surface is closed. `anon` holds zero privileges on every table in `public`, `authenticated` is reduced to SELECT/INSERT/UPDATE/DELETE, and default privileges are fixed so new objects do not re-grant.
DATE: 2026-07-26
WHY: The read finally ran after three sessions of carrying it. `anon` held SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES, TRIGGER and MAINTAIN on fourteen of fifteen tables; `families` was clean only because it was the one table anyone had ever revoked, on 07-25. THE REFRAME THAT MATTERS: this was not fourteen instances of drift, it was the Supabase default — GRANT ALL to anon and authenticated with RLS as the intended gate. So the real question was never "who broke these" but "is RLS sound against anon on all fifteen," and it is: all 15 RLS-enabled, exactly four anon-reachable policies, all RESTRICTIVE false denials, nothing permissive, no policy targeting PUBLIC/ALL. THE REVOKE WAS STILL CORRECT, AND THIS IS THE PART WORTH REMEMBERING: RLS gates SELECT/INSERT/UPDATE/DELETE and nothing else. TRUNCATE, TRIGGER, REFERENCES and MAINTAIN were held by anon on fourteen tables with nothing whatsoever in front of them. Not reachable through PostgREST, so theoretical — but "RLS is the sole gate" was only ever half true, and that is the kind of sentence that ages badly in a doc. REJECTED: writing a revoke list off the first read without checking RLS first — that is the 07-16 outage in reverse, where fourteen tables lost their authenticated grants and the roster went blank. The evidence that made the revoke safe was the `families` revoke on 07-25 surviving with no breakage, which works because pre-login flows route through SECURITY DEFINER RPCs that execute as owner and ignore table grants entirely. Verified from pg_class.relacl rather than from the migration, and confirmed on the glass: the logged-out signup screen renders and resolves the founder gate with an anon session against a system_flags table that now grants anon nothing.
REPLACES: Nothing — new decision. Closes the open item created 2026-07-16 and re-priced 2026-07-25.
STATUS: LOCKED

---

DECISION: Grant drift downgrades from a recurring class-level defect to a bounded platform residual.
DATE: 2026-07-26
WHY: Six instances of grant drift over three weeks were all the same mechanism: Postgres re-grants by default on newly created objects, and every fix this project had made was a reactive one-off against a default that re-asserts itself automatically. The 07-26 migration altered default privileges for the `postgres` owner, so objects created by migrations no longer auto-grant anon. That is the first fix aimed at the mechanism instead of the symptom. WHAT REMAINS AND WHY IT CANNOT BE FIXED: the `supabase_admin` default-privilege entry is unchanged, because `postgres` is not a member of that role and the ALTER hit an exception handler. It fires only for objects `supabase_admin` itself creates — extensions and platform-managed schema work — not for anything in this project's migration path. It is unreachable from this connection. CONSEQUENCE: the standing rule changes shape. It is no longer "revoke reactively, forever." It is "re-read table grants after any Lovable platform-level change." Much cheaper, and bounded for the first time.
REPLACES: Supersedes the standing framing of grant-drift as an open recurring defect. The six historical instances remain accurate as written.
STATUS: NOTED

---

DECISION: `families.timezone` is write-once with no update path, and that is a defect, not a design.
DATE: 2026-07-26
WHY: Timezone capture shipped on 07-26 inside `complete_household_setup`, validated against pg_timezone_names with a fallback to America/Los_Angeles. Recon then established that it is written ONLY on the families INSERT in the create branch. There is no UPDATE path anywhere — not for a user, not for an admin, not through any RPC. TWO CONSEQUENCES, BOTH REAL: the thirteen existing holds are pinned to the default forever, and any hold created while its owner is travelling is permanently wrong with no recourse. The 07-23 fix made the household clock local; this makes it household-PERMANENT. Recorded as NOTED rather than fixed because the fix is small but needs a home — an RPC plus somewhere in hold settings to call it from — and inventing that surface inside an onboarding build would have been scope creep. NOTE ALSO, because it reads as a bug and is not one: `profile.tsx` calls the same RPC, which initially looked like a path that could overwrite a correct stored timezone with a traveller's browser value. It cannot, precisely because there is no update path. The absence of the feature is what makes the second call site safe. Gate B item.
REPLACES: Nothing — new decision. Partially closes "household timezone capture at setup."
STATUS: NOTED

---

DECISION: Unapproved weekly and monthly quests never roll forward. This is a guilt pile across two of the three cadences.
DATE: 2026-07-26
WHY: `roll_missed_dailies()` filters on `recurrence = 'daily'` and nothing else in the system rolls anything. Weekly and monthly successors are minted exclusively by `handle_quest_approval()`, which fires on the approval transition. Therefore a weekly or monthly instance that is never approved sits past-due on the board permanently and accumulates. Two are stranded on the live board right now: "Grocery Shopping" (due 07-06, claimed) and "Take out the trash" (due 07-21, submitted). THIS DIRECTLY CONTRADICTS THE NO-GUILT-PILE PRINCIPLE, which the daily rollover exists to enforce and which was independently arrived at twice — once here and once in Endure. WHY IT WAS MISSED: on 2026-07-21 a client-invoked roll-forward job for weekly and monthly was scoped and then cut as redundant. That call was correct for APPROVED quests, where the trigger already handles succession, and completely blind to unapproved ones. A correct analysis of one branch was applied to both. LIKELY SECOND PAYOFF: these two rows are the only past-due unarchived quests on the board, and the STALE chip appears on the weekly but not the dailies — so if STALE reads `due_date < today`, one fix may close both items. Verify that before building anything.
REPLACES: Amends the 2026-07-21 call that a weekly/monthly roll-forward job was redundant. That entry stands for approved quests and is wrong for unapproved ones.
STATUS: NOTED

---

DECISION: Recurring anchors are computed by date_trunc and deliberately discard the user's chosen day. The recurrence chip must say so.
DATE: 2026-07-26
WHY: `handle_quest_approval()` computes weekly as `date_trunc('week', household_today) + 1 week` and monthly as `date_trunc('month', household_today) + 1 month`. So every weekly successor lands on a Monday and every monthly successor lands on the 1st, regardless of what date the user originally picked. This is the 2026-07-14 fixed-calendar-anchors decision working exactly as designed, and it is confirmed by data: all four monthly quests have been approved, each produced exactly one successor dated 2026-08-01, each successor's created_at matches its parent's completed_at to the microsecond, no duplicates and no orphans. A CONCERN WORTH RECORDING BECAUSE IT WAS CORRECT IN SHAPE AND WRONG IN FACT: jAIne flagged a potential clamp-and-drift failure — Jan 31 plus one month is Feb 28 in Postgres, and if the anchor were re-derived from each previous instance a monthly set on the 31st would drift to the 28th and never climb back, silently and permanently. That failure is structurally impossible here because the day-of-month never enters the arithmetic. It is worth keeping because it is exactly what WOULD happen under the intuitive implementation. THE USER-FACING CONSEQUENCE IS THE REAL FINDING: a monthly created on the 15th recurs on the 1st, and nothing in the UI ever says so. The recurrence chip reading "Monthly · 1st" and "Weekly · Mon" stops being cosmetic polish and becomes the only place the app tells the truth about what it just did.
REPLACES: Nothing — new decision. Confirms and documents the 2026-07-14 fixed-anchor decision against live data.
STATUS: LOCKED

---

DECISION: The mascot is Pip. Full stop.
DATE: 2026-07-26
WHY: Cinder (kid vote) versus Holt (lore) sat as an open decision in parking-lot for weeks while "Hi, I'm Pip — let's light your hearth" was already live on the auth screen and had been for some time. On 07-26 Pip's name went onto five onboarding screens. The decision was effectively made by deployment, and leaving it open only risked someone acting on the parking-lot line and starting a rename across a growing surface. Scott's call, immediate and unambiguous. REJECTED: Cinder and Holt, both of which are fine names attached to a character that already has one.
REPLACES: Closes the "Mascot name — Cinder vs Holt" open decision in parking-lot.
STATUS: LOCKED

---

DECISION: First-run onboarding is a five-screen Pip-guided flow inside a dedicated SetupShell. It is guided doing, not a feature tour, and not a single screen.
DATE: 2026-07-26
WHY: Setup ended at roster creation and stranded the user on an empty board — they had members but had never created a quest, minted an ember, or redeemed anything, meaning they had not performed the core loop even once. The flow is add-family, post your first quest, stock the Vault, the rest of your hold, the handoff, then land on a populated board. Its job is to make the pitch TRUE rather than claimed: chores become quests, quests earn embers, embers buy real rewards. Screens 1 and 2 build both halves of the loop with the household's own names in it; screen 4 hands off the half that cannot happen on a screen, because embers minting requires a second person and a real day. THE PROCESS FAILURE THAT MAKES THIS ENTRY NECESSARY: this was decided on 2026-07-12. A decision block was drafted in chat — "Pip's guided first-run is a GUIDED LOOP, not a feature tour" — Scott then pushed past it in the same session to the multi-screen setup-flow shape, and NONE OF IT WAS EVER COMMITTED TO decisions.md. status.md compressed the entire discussion into "four payloads, one screen," and on 07-26 jAIne built to the compression, producing a single board doorway instead of the flow Scott had specified two weeks earlier. Scott caught it. That is the seventh instance of state lives in the repo or it doesn't exist, and it is the most expensive one so far because the loss was a design, not a fact. ARCHITECTURE, AND WHY IT WAS SPLIT IN TWO: recon established there was no reusable setup shell — auth, onboarding and board were independent routes with no shared container and no step state. Building the shell and the content as one job would have buried a risky 500-line refactor inside 800 lines of new screens. Splitting it made the shell independently verifiable and turned "did adding four screens require changing the shell" into a one-line verdict on the previous build. The answer was no, which is what a registry is for. REJECTED: an overlay tour with coach marks and spotlight state, which is a step-state machine you own forever; and touring Lists, Calendar or Campaigns as functional screens, which is a membrane violation — they are utilities and walking a first-time user into one dilutes the only loop that matters.
REPLACES: Supersedes the never-committed 2026-07-12 draft and the "four payloads, one screen" framing carried in status.md.
STATUS: LOCKED

---

DECISION: Lists, Calendar and Campaigns get their own screen in the first-run flow. Named and shown; never explained, never entered.
DATE: 2026-07-26
WHY: Scott overruled jAIne here and was right. jAIne had demoted these three to a footer underneath the handoff screen, on membrane grounds. Two things were wrong with that. First, a footer under a handoff is where things go to be ignored — if they are worth showing at all they are worth a beat, and if they are not worth a beat they should not be in the flow. Second, it quietly reversed what Scott had already specified on 07-12, which was a screenshot overview of the rest of the hold and how to reach it. THE MEMBRANE LINE STILL HOLDS AND IS UNCHANGED BY THIS: touring a utility is the violation, naming one is not. Seen, named, not used. No "tap here to make your first list," no explanation of how any of them work, no entry point out of the flow. The screen's only job is to stop the mental model from setting as "chore app," so that in three weeks when the household needs a grocery list, Emberhold is a candidate. ORDER IS LOAD-BEARING: this screen sits second-to-last and the handoff stays last, because the final thing a user reads has to be the thing they do next. Ending on a feature overview ends on browsing.
REPLACES: Supersedes jAIne's footer amendment within the same session. Restores the 2026-07-12 shape.
STATUS: LOCKED

---

DECISION: The first quest may be assigned to a named hearthmate OR posted open to anyone. Assignment is the preference, never a requirement.
DATE: 2026-07-26
WHY: jAIne briefed assignment as mandatory, arguing that a first quest with a kid's name on it makes the hold feel real rather than like a demo. Scott overruled after seeing it on the glass, and the reasoning is better on two independent grounds. THE PRODUCT REASON: the board has an Open Bounties strip, so forcing assignment on the very first quest teaches a household that quests belong to people rather than that work can be claimed — the wrong first lesson for a bounty-shaped family. THE STRUCTURAL REASON: add-family is skippable, so a hold can legitimately reach the first-quest screen with no other hearthmates to assign to. jAIne's instruction had no answer for its own edge case, and would have rendered an empty or single-option picker. Treatment: named hearthmates remain visually primary, with open-to-anyone as a full-width peer option rather than a downgrade. If the hold has no other hearthmates, default to open and say so in one line rather than presenting a choice that isn't one.
REPLACES: Supersedes jAIne's assign-only instruction within the same session.
STATUS: LOCKED

---

DECISION: The setup flow is one-directional. Skip always exits to the board and never advances. There is no back navigation.
DATE: 2026-07-26
WHY: Two separate calls that ended up as one property of the shell. SKIP: the pre-existing add-family behavior was already correct — "Skip — I'll add them later" navigated straight to /board with replace:true rather than advancing to a next step. That is preserved and, better, centralized: skip is now a single affordance owned by SetupShell, so no future screen can implement it wrongly because no future screen owns it. This matters more as the flow grows, because a long flow whose escape hatch advances instead of exiting makes leaving feel like work, and that is how a guided first run turns into a hostage situation. BACK: declined for now. One-directional is simpler, nothing in the flow is destructive, and each screen writes on completion so nothing is lost by leaving. The accepted cost is that a typo'd quest title cannot be corrected until the user reaches the board. RELATED AND LOAD-BEARING: each screen writes on completion rather than batching at the end. Bail at screen 3 and the quest and the reward already exist. Verified on the glass by skipping at screen 2 and confirming screen 1's quest survived.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Onboarding screenshots are captured manually from a seeded demo hold. The placeholder state is the shipping state.
DATE: 2026-07-26
WHY: Screen 3 shows Lists, Calendar and Campaigns, which needs three images. Three options were assessed and two were rejected. REJECTED — SCRIPTED CAPTURE: Claude Code priced Puppeteer automation at 2–4 hours against 15–20 minutes of manual setup, for three PNGs that will be re-taken perhaps twice a year. Scripting only pays off if the UI churns constantly, and it does not. REJECTED — LOVABLE-GENERATED IMAGERY: Lovable can generate images, but what this screen needs is a screenshot of the actual surface. A generated approximation of your own UI reads as a mockup, and it actively misleads a first-time user about what they are about to open the moment it drifts from the real screen. WHY A DEMO HOLD IS REQUIRED AT ALL: the real household cannot be the source, because that is Scott's kids' names and faces shipping to strangers. A fresh hold is the opposite problem — three empty screens that sell nothing. So a plausible fake hold is a prerequisite, and it pays for itself again at Gate D as landing-page assets and as a permanently safe place to screenshot from. THE SHIPPING RULE: screen 3 ships with zero images and stays that way until real ones exist, so the empty state is not a temporary eyesore to be tolerated — it is what a stranger sees on day one and it must read as deliberate and finished. Labelled frames, never broken-image boxes.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Signup collects EMAIL AND PASSWORD ONLY before email verification. The pre-verification localStorage stash is deleted.
DATE: 2026-07-26
WHY: Signup collected name, avatar, role, PIN, hold name and create-vs-join before verification, stashed them in localStorage under "emberhold.pending_setup", and replayed them after confirmation. Users reported being asked for everything twice. THE MECHANISM IS STORAGE PARTITIONING, NOT BROKEN CODE: the stash works perfectly and its values are forwarded verbatim into complete_household_setup and set_profile_pin — but localStorage is scoped per storage partition, so it can only be read from the context that wrote it. Returning to the original tab and signing in is definitionally the same partition, so that path was always seamless. Tapping the confirmation link opens in the mail client's in-app webview, a different partition, so loadPending() returned null and the user retyped everything into a form the code already had built. That is the same wall as cross-device signup, just further apart. WHY DELETION IS SAFE: recon confirmed the finish-setup form already collects the identical fields and calls the identical RPCs, and that PENDING_KEY, PendingSetup, loadPending and savePending are referenced nowhere outside auth.tsx. Nothing downstream loses a required input; it simply always arrives from the second collection point instead of sometimes from the first. THE CONSEQUENCE, ACCEPTED DELIBERATELY: users who confirm in the same browser lose a no-retyping shortcut. That shortcut was already the minority case given mailer_autoconfirm=false and the prevalence of in-app webviews on mobile. One round of entry after verification beats two rounds where the first is discarded. TWO ITEMS CLOSE AS SIDE EFFECTS: the adult PIN was written to localStorage in plaintext inside the PendingSetup blob and cleared only on a successful runSetup, so in the failing path it persisted indefinitely — no stash, no plaintext, and that open security item closes by deletion rather than by fix. And cross-device signup, pending verify since the hiatus, stops being a special case because there is no client-side state to strand. A CORRECTION RECORDED SO IT DOES NOT PROPAGATE: jAIne claimed this fork determined whether a new household saw onboarding at all. That is false. runSetup() always routes a create signup to /onboarding/add-family regardless of data source. The bug was retyping, only retyping, and jAIne asserted a larger failure than the evidence carried before building a recommendation on it.
REPLACES: Nothing — new decision. Closes "Adult PIN plaintext in localStorage during signup."
STATUS: LOCKED

---

DECISION: Quest creation stays ungated. What changes is the CURRICULUM — onboarding never teaches quest authoring to a non-approver. The join path never offers a PIN.
DATE: 2026-07-26
WHY: Recon found quest creation gated nowhere — no client check, and the RLS INSERT policy on quests scopes to family_id = current_family_id() and created_by = auth.uid() and stops there, in explicit contrast to the UPDATE and DELETE policies and enforce_quest_update_authority, all of which check parent. jAIne first recorded this as NOTED-leave-it-alone; Scott then called for an adult gate; jAIne over-converted a one-line call into a LOCKED decision with an RLS clause, four entry-point changes and role conditions on two onboarding surfaces; Scott then drew the distinction that settles it. THE DISTINCTION, WHICH IS THE WHOLE ENTRY: "can a kid create a quest" and "should a kid be TAUGHT to create a quest" are separate questions, and only the second one needed deciding. The first is a permission change touching the busiest table in the app, with real risk and no evidence behind it. The second costs nothing, is fully reversible, and delivers the actual intent. THE CALL: leave the permission alone. Approval is the real gate and it holds — a kid can propose a 500-ember quest and cannot cash it, so the economy was never exposed. Onboarding simply does not teach authoring to anyone who is not an approver. ABSENCE, NOT A LOCKED DOOR: this is strictly better than gating, because a gate means a kid eventually finds a button that refuses them — the "where'd my chore go" failure aimed at exactly the person the app exists to be fun for — while absence teaches role by never raising the subject. It also avoids the trap gating would have created: FirstQuestDoorway and the onboarding first-quest screen both render for kids, so a permission gate without matching role conditions walks a kid into a wall behind a friendly mascot. THE DOWNSTREAM CONSEQUENCE, AND IT IS THE USEFUL PART: the joiner onboarding therefore has TWO FLAVORS, split by role. An adult joiner is an approver arriving into someone else's hold and needs approving, the Vault as an operator, and what the PIN is for — quest authoring belongs there. A kid joiner needs claiming, completing, submitting and spending. This is not a new mechanism; it is ROLE-AWARE DEPTH, already a stated discipline: same data, different lens, filters never separate systems. OPEN AND CHEAP: nobody has checked whether quest creation is DISCOVERABLE from a kid's board or merely reachable. The FAB in BottomBar.tsx plus entry points in board.tsx, campaign.$id.tsx and QuickAddTray.tsx are unconditioned, so structurally a kid can get there — but what a kid actually sees is unread. One line of recon, and it should be answered before anyone reopens gating. THE JOIN + PIN BUG, FIXED SAME SESSION AND RELATED BY SUBJECT: runSetup called set_profile_pin whenever role === 'parent' && pin, but complete_household_setup always creates a joining member as kid/pending regardless of requested role, so a joining adult who set a PIN triggered "Only parents can set a PIN" server-side and got a generic error toast at the end of signup — firing for every joining adult in every household. The join path now renders no PIN field, and set_profile_pin fires only when familyChoice === 'create'; a joining member's PIN is set after an existing parent admits them.
REPLACES: Nothing — new decision. Settles a permission that was inherited rather than chosen, by deciding not to change it.
STATUS: LOCKED

---

DECISION: The membrane amendment's currency entanglement is CLOSED. Fitness cannot mint embers, so the one-game-per-module amendment stands on its own.
DATE: 2026-07-25
WHY: The 2026-07-25 membrane amendment ("each module may carry at most ONE game, sealed") closed with a NOTE deferring itself: "if training simply mints embers, this amendment may be unnecessary. Decide the currency first." The currency is decided and the escape hatch is closed. Fitness CANNOT mint embers — a kid's ember requires adult verification and nobody can verify a squat set, so minting fitness embers would either make the approval gate a rubber stamp (corrupting what approval means everywhere, including chores) or breach the minting rule outright. The amendment therefore stands on its own merits and is not contingent on anything. Recorded because decisions.md is append-only: that NOTE is permanent, it reads as an open question, and a cold reader six months out would otherwise re-open a settled call by following it. ALSO NOTE, for the same reason: the membrane amendment and several neighbouring entries use "layer" and "training," both retired the same day. Append-only means they stay as written; the vocabulary entries govern.
REPLACES: Closes the open NOTE in "AMENDMENT to the membrane" (2026-07-25). The amendment itself is unchanged and remains LOCKED.
STATUS: LOCKED

---

DECISION: The four registers are AESTHETIC ONLY. They are not functional areas. Fitness is the sole module.
DATE: 2026-07-25
WHY: Supersedes the same-day call that the four avatar registers were four functional layers. That reading failed decomposition, and the decomposition IS the argument: Keep (upkeep, chores, repair, DIY) is quests. Garden (yard, planting, outdoor maintenance) is quests. Hall (celebration, hosting, parties, feasting) is lists, campaigns, and calendar events. All three are content and theme sitting on machinery that already exists — which is the definition of a register, not a layer. Only fitness failed to decompose, and it failed structurally rather than thematically: IT CANNOT MINT EMBERS WITHOUT BREAKING THE MINTING RULE, because a kid's ember requires adult verification and nobody can verify a squat set. That is why fitness is the odd one out and why it is the only module. Scott ran this decomposition himself and it took one message. REJECTED: keeping four charters with three of them stubbed — writing law for areas that turned out to be themes is fan fiction in a canonical doc, and a stub invites someone to fill it. The identity-inheritance argument that carried the original call still stands and is unaffected: the four registers are paid-for identity work (four palettes, four icons, four ambient themes, 48 avatars, a live members.class enum) and they keep every bit of that job. They just don't also run the household. ALSO WRONG IN THE ORIGINAL, INDEPENDENT OF THE SUPERSEDE: it defined "HALL = cooking and feeding," which directly contradicts the LOCKED avatar-class definition capping food at roughly one third of the register — a constraint that exists because Hall was renamed FROM Feast on 7/03 precisely to escape a food narrowing. It re-narrowed to Feast eleven days later inside a doc whose own string law forbids it.
REPLACES: Supersedes "The four avatar registers are the four functional modules of Emberhold" (2026-07-25), now marked SUPERSEDED in place.
STATUS: LOCKED

---

DECISION: CONSTITUTIONAL — NO CONVERSION. Currencies may be multiple; they may never exchange across a module boundary.
DATE: 2026-07-25
WHY: Replaces "two backbones: one currency (embers)." That rule was a proxy that worked exactly as long as there was one functional area, and it broke the moment a second one was proposed — which is how it got discovered. The load-bearing thing was always the absence of an EXCHANGE RATE, not the absence of plurality: a foreign-exchange desk inside a chore app is the failure mode, and two sealed economies sharing a login are not that. The semantic argument is the stronger half and is why this is an improvement rather than a loosening: an ember means "an adult approved this, and it buys me a real thing." That meaning is tight, legible, and is what gives the Vault its teeth. If fitness minted embers, an ember would degrade to "I did something," and a kid could farm ice cream with push-ups. A second currency exists to keep EMBERS CLEAN, not to add a wallet. Scott made this argument and jAIne had it backwards, defending "one currency" as the invariant when jAIne's own stated objection had already named the real one. GUARDRAIL so this doesn't become four currencies: a module earns one only with (a) its own distinct earn, (b) its own distinct sink, and (c) no conversion path — miss any one and what it wants is a counter, not a currency. Recorded as standing law with nothing currently to govern, deliberately: this is the only moment a rule like this is cheap, because nobody is arguing for a specific exception yet.
REPLACES: Amends the "two backbones / one currency" principle in master-spec.md and north-star.md.
STATUS: LOCKED

---

DECISION: CONSTITUTIONAL — Minting requires an APPROVAL-AUTHORIZED ACTOR. Approval authority is adult-only.
DATE: 2026-07-25
WHY: Rescopes "embers mint only on adult approval," which was a household rule stated as a platform rule. CORRECTS AN EARLIER PHRASING FROM THIS SAME SESSION that said "real-world redeemability requires a SECOND PARTY to mint." That was wrong and Scott caught it: adult self-completion writes status='approved' + approved_by=self atomically, which is deliberate, shipped, and in the spec — an adult mints with no second party anywhere in the transaction. The rule is about AUTHORITY, NOT PARTY COUNT. What a_enforce_quest_update_authority actually enforces is that the approver is an adult, and it always has been. For a kid, satisfying that necessarily means a second person; for an adult it is satisfied by themselves, and the gate is SATISFIED, not bypassed. THE RESIDUAL HOLE IS REAL AND DELIBERATELY UNCLOSED: an adult can mint themselves unlimited embers, and would be buying "pick dinner" and "one hour alone" — household-internal favors with no external cost. An adult lying to themselves is not a problem this app should solve. THE KID CASE IS WHERE IT BITES, AND IT IS THE WHOLE ARGUMENT FOR THE FITNESS MODULE: a kid's ember requires an adult to verify, and nobody can verify a squat set. Either the approval gate becomes a rubber stamp — corrupting what approval means everywhere, including chores — or fitness effort does not mint embers. It does not. A sealed in-app-only currency needs no gate at all, because it buys nothing outside the app and the only victim of an inflated count is the squat. REJECTED: requiring verification everywhere for consistency — it fails the calibration check badly (this is a family chore game with your kids' faces in it) and would make a garage workout unloggable.
REPLACES: Amends "the core rule: embers mint only on adult approval" in master-spec.md — narrows it to its real scope. Also corrects the "second party" phrasing drafted earlier in this same session, which was never committed.
STATUS: LOCKED

---

DECISION: Cross-currency conversion in any direction is DECLINED, permanently and in advance.
DATE: 2026-07-25
WHY: Recorded before anyone asks, because the seal in the no-conversion rule will not break with a bang — it breaks in a reasonable-sounding feature request. The two that will arrive, and both are declined: (1) "can a module currency buy a Vault reward?" and (2) "can a kid trade module currency for embers?" The second is close to guaranteed, because "adults only" is already logged as a proxy for "participates" that will not hold — a twelve-year-old lifting in a garage gym is normal, so a kid will eventually be earning module currency and will make the case in person. Writing this down now is cheaper than relitigating it in six weeks with a kid standing there. WHY IT MATTERS MECHANICALLY: the moment a rate exists, so does farming — the cheapest path to ice cream stops being chores and becomes whichever activity converts best, and the entire ember economy is now downstream of a workout log nobody approves.
REPLACES: Nothing — new decision.
STATUS: DECLINED

---

DECISION: Module enablement is offered at the END of onboarding, is a BRANCH rather than a toggle, and is reversible by an adult from a Hold profile screen.
DATE: 2026-07-25
WHY: Amends the same-day enablement decision, which established household-level intent-based enablement but got three things wrong or unspecified. (1) THERE IS NO MENU. With three of the four proposed areas revealed as registers, "what does this Hold want" was always one question with two answers: Emberhold, or Emberhold with Forge. (2) IT IS A BRANCH, NOT A FLAG. Selecting Forge does not set a value and continue — it launches a second setup flow (equipment, experience, goals). jAIne argued for moving the question out of onboarding entirely, to a card after the first quest is posted, on the grounds that onboarding's job is activation and a fitness toggle competes for the most expensive screen real estate in the product. Scott overruled, correctly: a settings toggle that fires a multi-screen wizard is worse than either option, and second flows belong at the end of first flows. (3) SEQUENCE, AND THE ORDER IS LOAD-BEARING: hold setup, then the Forge question, then Forge setup if selected, then the first-quest doorway LAST. The activation doorway stays last so momentum carries straight into a populated board; a five-screen detour wedged between "post your first quest" and "see it on the board" breaks the one handoff that cannot afford it. Adult-gated because enablement is hold configuration and therefore admin authority, consistent with join-code visibility and invite. Everything load-bearing in the original is untouched: household-level never per-member (the same logic that makes per-kid SKUs unthinkable), written only through the RPC so families never gains a table-level UPDATE grant (which would re-open the is_founder paywall hole), and never offer a module that does not exist.
REPLACES: Amends "Module enablement is household-level, chosen at onboarding by intent, soft and reversible" (2026-07-25).
STATUS: LOCKED

---

DECISION: VOCABULARY — "layer" is retired. The structural nouns are HOLD, SURFACE, MODULE, and REGISTER.
DATE: 2026-07-25
WHY: The word was coined to name four functional areas. Three turned out to be registers and the fourth is a module, so the noun described a category with one member — and an extra structural noun that buys nothing is exactly the drift the string law exists to prevent. THE DEFINITIONS: the HOLD is the tenant (families in schema, always "the Hold" user-facing). A SURFACE is a platform screen every hold gets — board, Vault, campaigns, calendar, Lists, Briefing, wall. A MODULE is an optional functional area a hold may enable; there is exactly one, fitness. A REGISTER is one of Forge/Garden/Keep/Hall — an avatar class and an ambient theme, aesthetic only, gating nothing. CONSEQUENCE WORTH RECORDING: families.enabled_modules is CORRECTLY NAMED AS SHIPPED and needs no migration — ['household'] is base, ['household','fitness'] is base plus Forge. An earlier recommendation in this same session to rename it to enabled_layers was predicated on there being four layers and evaporates with them; it is recorded here so nobody acts on the old advice. A text[] holding a current binary is mild over-engineering that costs nothing and stays honest if a second module ever appears. REJECTED: renaming the four registers to break the shared naming — the names are inherited identity work and the apparent collision disappears once "register" is defined as aesthetic-only.
REPLACES: Nothing — new decision. Retires terminology introduced and discarded the same day.
STATUS: LOCKED

---

DECISION: The fitness module's names — stored value `fitness`, product name FORGE on screen. "Training" is retired.
DATE: 2026-07-25
WHY: Three words were circulating for one concept — "fitness" in conversation, "training" in the enabled_modules allowed set, "Forge" as the register — and one of them was going to end up on a screen by accident. Scott's call: FITNESS is the right concept word, universally understood, needing no explanation to a stranger, and describing the domain rather than one activity inside it. FORGE is the product name and what appears on screen — it is the name of the fitness home inside Emberhold. This is the same pattern already in use: the schema says families, the UI says "the Hold." Concept in the data, product on the glass. THE FORGE REGISTER AND THE FORGE MODULE SHARING A NAME IS NOT A COLLISION, and jAIne's earlier flag is withdrawn: "warriors, protectors, ember-forged heroes" and the place you go to lift point the same direction — same word, same meaning, which is what a good name does. The flag was legitimate while Forge was a proposed HOUSEHOLD functional area, where the two senses genuinely diverged; it died with the layer model. ONE CODE CONSEQUENCE, FREE TODAY: set_enabled_modules() validates against a hardcoded allowed set containing 'training'. One string to 'fitness'. Nothing has ever written that value and all thirteen households read ['household'], so it costs nothing now and becomes a data problem the moment onboarding ships. Existing decisions.md entries retain "training" as written — the file is append-only and a string-law change gets a new entry rather than a retroactive edit.
REPLACES: Nothing — new decision. Retires "training" as a user-facing or stored term.
STATUS: LOCKED

---

DECISION: The Forge's SHAPE is an open decision. Option A (tool only) ships before Option B (tool plus one sealed game) is designed.
DATE: 2026-07-25
WHY: The stated goal is to replace Fitbod, save the subscription, and add some family cohesion from being on one app. Nothing in that goal requires materials, artifacts, or a collection — it requires set logging, automatic programming, rest timing, progressive adjustment, and the reason string. OPTION A is a clean utility with no game and no currency, writing presence to the activity feed and nothing else. OPTION B additionally carries one sealed game: effort produces typed materials, materials combine into artifacts, artifacts accumulate as legible evidence of what was actually trained. B IS DEFERRED, NOT REJECTED. The reason is precedent, not taste: the standalone training app collapsed the same day on PROPORTION — it had grown a tool wall, a metal-composition system, and project-scoped blocks while still being unable to log a working set. The materials-and-artifacts idea is that identical shape and is currently the most interesting part to build, which is the tell. WHAT B OWES BEFORE IT CAN BE WRITTEN AS TRUTH: whether materials pool or are claimed at commitment; what they are typed ON (typing by modality contradicts a LOCKED call in endure-canonical that all modalities produce equal value, and with one available modality there is nothing to type across anyway); and whether a collection survives the calibration check. THE MONEY ARGUMENT FOR A, WHICH IS THE STRONGEST ON THE BOARD: Fitbod is $15.99/mo, roughly $192/yr against a $636/yr break-even target — about a third of break-even recovered with no paywall, no funnel, and no Gate E dependency.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The Forge's free/paid split. Deterministic features are FREE; only LLM-generated programming is PAID.
DATE: 2026-07-25
WHY: Applies the amended monetization principle ("free is a full tool; paid is delight, OR marginal cost") to its first real case, and the application is narrower than it first looked. FREE: set logging, rest timing, progressive adjustment, deterministic autoregulation, and template-driven automatic programming. These run on rules, not tokens, and have no marginal cost — the guardrail in the original amendment says explicitly that paywalling a rest timer would be indefensible, and template-driven programming is the same category. PAID: LLM-generated programming only, the sole feature with a genuine unavoidable per-user cost, and one the market has already trained people to expect to pay for. THE GUARDRAIL RESTATED BECAUSE THIS IS EXACTLY WHERE IT ROTS: the cost must be real, not architected into existence. If a feature could run as a deterministic rule and a model call was added to justify a paywall, that is the rule being gamed against itself.
REPLACES: Nothing — new decision. Applies the 2026-07-25 monetization amendment.
STATUS: LOCKED

---

DECISION: Endure is not merged into Emberhold. The Forge borrows Endure's PRINCIPLES and none of its systems.
DATE: 2026-07-25
WHY: Endure is a separate, substantially designed fantasy-progression product with its own canonical repo. The blocker is structural, not preferential: its fitness bridge requires Apple Health and Google Fit with heart rate as gatekeeper and validator, THERE IS NO HEALTHKIT WEB API, and the PWA posture is LOCKED (Capacitor assessed and DECLINED 2026-07-22). Strider, Unbroken, and most of Mystic are therefore unmeasurable in Emberhold — not hard, unavailable. Self-logged strength work is the only modality a PWA can honestly measure, which is why the Forge is strength-only and why the entire hybrid layer (the largest locked doc in the Endure repo) is dead on arrival here: hybrids require sustained overlap across two paths, and one path has no overlap. WHAT DOES PORT, PRINCIPLES ONLY: (1) never decays, never punishes absence, no failure states — which Emberhold already does independently via no-guilt-pile daily rollover, the same instinct arrived at twice in two projects; (2) recognition, not reward — the scale is invisible and the player experiences only what it produces; (3) "destroying loot is not failure, it is authorship" — the player decides what stays; (4) EXPRESSION vs MARKS — expression is choice and always free and reversible, a mark is memory and what is paid for is the ritual of permanence. That fourth one is a sharper monetization test than "delight vs function" and is folded into master-spec: is this thing a CHOICE or a MEMORY? EXPLICITLY NOT PORTED: loot tables, blueprints, ten identity pools, clans, five construction phases, drift ceremonies, aspect states. WHAT THIS DOES NOT DO: it does not kill Endure. Endure was blocked on needing a native app before this session; that block is unchanged and this decision neither creates nor resolves it. Endure's HealthKit requirement does NOT constitute a third Capacitor reopen trigger — the two named triggers stand alone.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The "proposed" status prefix is retired. jAIne sets the status; the conversation is the ratification.
DATE: 2026-07-25
WHY: The "jAIne proposes, Scott sets final" rule produced 27 entries stuck in limbo across 99 total, going back to 2026-07-18, plus 3 malformed entries carrying literal template brackets. The status field was never doing the work it claimed — it was recording that jAIne had not been told no yet. WORSE, THE RULE WAS ALREADY DEAD: it was discarded 2026-07-18 and recorded in parking-lot as "discarded, never canon," and jAIne kept generating proposed statuses for a week anyway — including in the same session where jAIne regenerated the parking-lot file containing the line killing it. THE REASON IT SURVIVED ITS OWN DEATH: the rule lived in Scott's Claude project instructions, not in the repo, so every fresh jAIne read it on cold open and started proposing again. A repo cannot overrule a system prompt. The instructions were edited the same session; without that edit this decision would have regenerated the problem within a week. THE REPLACEMENT, FOUR RULES: (1) jAIne sets the status, no prefix, ever. (2) decisions.md contains decided things only — if it is not decided it is not a decision and it goes to parking-lot OPEN DECISIONS, which gives the two docs genuinely different jobs instead of one being a waiting room for the other. (3) The conversation is the ratification: if it was talked through and the thread moved on, it is decided, and nobody confirms twice. (4) jAIne states the status out loud in the message text when delivering the block — a notification with an objection window, not a request for permission. WHY THIS IS SAFE: over-locking is cheap, because the fix is a supersede entry, and this session produced one that was better than the original it replaced. Never-locking is what was actually happening, and it cost a session partly spent re-deciding settled things. ALSO ADDED: SUPERSEDED as a real status value. Append-only means a wrong entry stays visible forever, and nothing previously distinguished "this is law" from "this was law for eleven hours."
REPLACES: Formally retires the "Scott sets every status" doc rule, discarded 2026-07-18 but operative in practice until now.
STATUS: LOCKED

---

DECISION: DECOMPOSE BEFORE YOU PROMOTE. A proposed new structure must be decomposed into existing machinery before it is written into a doc.
DATE: 2026-07-25
WHY: Three of four proposed functional areas dissolved into quests, lists, and campaigns under inspection — in the same session they were proposed, and after a full master-spec regeneration had already been written around them. The check is cheap and mechanical: name the thing, then list what it would actually be built from. If the answer is entirely existing surfaces, it is content or theme, not architecture. THE GENERAL FORM: new structure earns its name by FAILING to decompose. Fitness earned it by being unable to mint embers without breaking a constitutional rule; Keep, Garden, and Hall did not, because they are quests and lists with a palette. RELATED AND ALREADY LOGGED: the same day produced "fetch the canon before producing anything" — both are instances of one failure, which is writing the doc before doing the check.
REPLACES: Nothing — new decision. Generalizes alongside, and does not supersede, the 2026-07-23 "sweep the class before fixing the instance" rule.
STATUS: NOTED

---

STATUS VALUES:
- LOCKED — decided and binding.
- DRAFT — decided, contingent on a named verification that hasn't happened yet. Name the verification.
- NOTED — a finding or observation that constrains future work without being a rule.
- DECLINED — rejected deliberately.
- SUPERSEDED — was law, no longer is. Name the date and the entry that replaced it.

jAIne sets the status. There is no "proposed" prefix.

---

DECISION: The four avatar registers are the four functional modules of Emberhold. FORGE = training; KEEP = the house maintained; GARDEN = growing; HALL = cooking and feeding.
DATE: 2026-07-25
WHY: Scott exercised veto here over jAIne's objection, and the argument that carried it is the right one: if the child layers ARE the registers, the naming isn't chosen, it's inherited. Emberhold already owns four palettes, four icons, four ambient themes, 48 avatars, and a `members.class` enum carrying these exact four values — that identity work is paid for and should be banked rather than duplicated. Two corrections landed inside the session and both stuck: (1) jAIne argued a forge is where you MAKE things so Forge should be DIY — Scott countered that iron and the gym are near-synonymous and the register is about heat and metal, which is the stronger read; (2) jAIne claimed DIY had no home, splitting "projects" from "maintenance" — Scott corrected that for an actual homeowner these are nearly the same set (deck, faucet, gutters, drywall), and recreational woodworking is the edge case, not the center. Keep absorbs both. REJECTED: naming the training layer as a standalone brand (Emberfit, Embermill, Emberworks, Ironhold) — moot once the layers became modules. The one structural asymmetry worth remembering: Forge is the only register about a PERSON; the other three are about the PROPERTY. That is why Forge is the right one to build first, and why Garden/Hall/Keep will sit much closer to Emberhold's existing surfaces — Hall in particular overlaps Lists and the calendar heavily.
REPLACES: Nothing — new decision. (Supersedes the working assumption, held for most of this session, that the training product was a separate app.)
STATUS: SUPERSEDED 2026-07-25 — replaced by "The four registers are AESTHETIC ONLY."

---

DECISION: The training layer is a MODULE inside Emberhold, not a sibling app. A separate Emberforge product, tenancy, and domain is DECLINED.
DATE: 2026-07-25
WHY: The separate-app frame got as far as a recommended domain purchase, an eight-table schema, and a working vertical slice before it broke. What broke it: Scott's wife, from the outside and with no context, said it no longer felt like part of Emberhold. jAIne's first response was a defense of family resemblance — same law (verification-minted currency, brightness=heat, absence-never-punished, free-is-a-full-tool, the same quest/approval schema, the same art floor). That defense is true and it was the wrong answer. The real signal was PROPORTION: in Emberhold the useful tool is the product and the game is a thin delightful skin; the training design had grown a tool wall, a metal-composition system, and project-scoped blocks while still being unable to log a working set. Merging fixes the ratio problem and deletes work: no second auth, no second tenant bootstrap (the standalone build deadlocked on exactly that — an RLS insert policy gating on a membership row that could only be created after the insert), one member table, one PIN system, one avatar pipeline, one Founding Guildhall unlock. REJECTED: separate app with merge-compatible naming (jAIne's hedge — real reasons: Emberhold's schema is in motion, blast radius on 13 live households, and two Lovable projects migrating one Supabase project breaks the one-writer rule). The hedge lost because merging NOW is a weekend and merging in three months is a migration project. Client-facing training (paying clients, not hearthmates) is deliberately left unresolved — a client is not a hearthmate and cannot share a household tenant; if it happens it is a separate trainer-facing surface reading the same data.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Module enablement is household-level, chosen at onboarding by intent, soft and reversible. Stored as `families.enabled_modules text[]`, written only through the `set_enabled_modules()` RPC.
DATE: 2026-07-25
WHY: One app that does chores AND fitness is the everything-app trap; one app that shows you only what you said you came for is a product. Onboarding-by-intent is the thing that turns the merge from bloat into focus — and it collapses into an item already at the top of the critical path, since the empty-board activation doorway and the "what does this Hold want" question are one screen. Household-level, never per-member, per the monetization seam rule — the same logic that makes per-kid SKUs unthinkable makes per-kid module toggles unthinkable. Reversible because people's answers change; onboarding TAILORS, it does not branch permanently. Ship two options (Household, Training) and never offer a module that doesn't exist — offering four is how the everything-app trap walks back in through the door built to keep it out. THE RPC IS LOAD-BEARING AND NOT A STYLE CHOICE: `families.is_founder` is service-role-write-only (LOCKED 07-19) and that is what makes the paywall enforceable. Granting `authenticated` table-level UPDATE on `families` so a screen could write a new column would have re-opened Finding B. The RPC means zero new grants on `families` at all — verified after the fact: `authenticated` still holds column-level UPDATE on `name` only.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: AMENDMENT to the monetization principle. "Free is a full tool. Paid is delight, OR marginal cost."
DATE: 2026-07-25
WHY: The LOCKED rule reads "free is a full tool; the purchase is delight, never access to basic function" — the membrane applied to money. Weekly AI-generated programming breaks it as written: that is function, not delight. It survives, but only as an amendment, because the honest justification is narrow and real — it is the one feature with a genuine per-user marginal cost. Emberhold's cosmetics have none, which is why paywalling a rest timer would still be indefensible. Scott's read, and it is correct: the market has already trained people that an AI tier costs money, so this is close to an expected place to pay. Generalizing the rule rather than granting an exception is deliberate — an exception invites the next case to be argued from scratch, while a clause gives a test for video hosting, exports, and anything else that bills per user. GUARDRAIL, so the clause doesn't rot: the cost must be real and unavoidable, not architected into existence. Anything can be made to "incur cost" by routing it through an API on purpose; if a feature could run on-device or as a deterministic rule and a model call was added to justify a paywall, that is the rule being gamed against yourself. Note the split this preserves: deterministic per-session autoregulation stays FREE, the weekly LLM plan is PAID.
REPLACES: Amends the LOCKED monetization principle in master-spec.md. Does not replace the monetization seam rule (household-level only), which is untouched.
STATUS: LOCKED

---

DECISION: AMENDMENT to the membrane. Each module may carry at most ONE game layer, and the layers are sealed from each other.
DATE: 2026-07-25
WHY: The LOCKED membrane reads "the game lives in quests only; other modules stay clean utilities that may optionally spawn a quest." A training module with a progression economy and a collection surface is a second game, not a utility — so either the training layer's game is declared the fitness module's quest-equivalent, or the principle is amended to be honest about what is being built. jAIne leans amendment because it says the true thing. What the amendment preserves is the part that was actually load-bearing: the membrane exists to stop EVERY surface becoming a game (gamify the chore, not the grocery item), and "at most one per module, sealed" keeps that intact while allowing a second module to have its own. Lists, Calendar, and meals remain clean utilities with no game at all. SEALED IS THE OPERATIVE WORD and it is where the real risk lives — the moment one module's economy converts into another's, there is an exchange rate, a farming exploit, and a foreign-exchange desk inside a chore app. NOTE: this amendment is entangled with the unresolved currency question (see parking-lot OPEN DECISIONS) — if training simply mints embers, this amendment may be unnecessary. Decide the currency first.
REPLACES: Amends the LOCKED membrane principle in master-spec.md and north-star.md.
STATUS: LOCKED

---

DECISION: A module never creates a person. Modules read hearthmates from the existing member table; there is exactly one member-creation path in the app.
DATE: 2026-07-25
WHY: The training prototype shipped a free-text "Who's training?" field that minted its own member rows. That is not a missing nicety, it is a defect class: two ways to make a person produces "Mia" in the hold and "mia" in the Forge with no reconciliation path, duplicated avatars, split history, and an identity model nobody can trust. The fix generalizes past this instance — any module that needs people reads them, renders them with the EXISTING hero-portrait component (so the avatar system, fallbacks, and identity colors stay consistent), and points at the existing member identifier. Empty state routes to Emberhold's add-hearthmate flow rather than offering a local shortcut. RELATED AND DELIBERATELY NOT SOLVED: filtering the training member list to Adults is a proxy for "trains," and it will break — a twelve-year-old lifting in a garage gym is normal. When it breaks, the answer is a per-member flag, not a role check. Not built now.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Schema is undone FORWARD, never by reverting the project. A Lovable version-revert is not a database revert.
DATE: 2026-07-25
WHY: Confirmed directly with Lovable when the option was live and attractive — nothing had been published and reverting looked like the clean escape. It is not. Version history restores code and project files ONLY: applied migrations are not rolled back, every table, column, and function stays live, and the migration FILES are removed from `supabase/migrations/` while their objects remain in the database. That is precisely the schema-history drift already recorded here as a five-times-observed landmine (and now six). Reverting would have manufactured a new instance to clean up an old one, and would have deleted the one artifact worth keeping. There is also no Lovable mechanism to roll back a specific migration — undoing schema means writing a new migration that reverses it. OPERATIONAL COROLLARIES, both earned tonight: drop in FK order without CASCADE, so an unexpected dependency stops the migration instead of being forced through; and run a read-only live-schema recon BEFORE the teardown, because the migration history does not describe the database. One caveat on that recon: Postgres records no DDL timestamps, so any "created today" list inferred from OID ordering over-reports — it dates objects relative to each other, never absolutely. Four functions were flagged as new that were 07-19/07-23 objects.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: The training progression engine is pure client-side TypeScript. Progression logic never lives in Postgres.
DATE: 2026-07-25
WHY: The engine takes last session's sets, the prescription, and the equipment inventory, and returns a load plus a human-readable reason. Pure in, pure out. It lives in the client for one non-negotiable reason: a garage has unreliable connectivity, and if the rule lives in the database the app is useless the moment wifi drops. Supabase is the sync target, not the compute. Secondary benefits that turned out to matter: it is trivially unit-testable (ten tests, every branch plus plate-snapping plus two-member independence), and because it has zero Supabase imports it SURVIVED the teardown of all eight tables completely intact — the only artifact of the night that did. Tests are worth insisting on here specifically: a silent off-by-one in progression corrupts training data that cannot be reconstructed. THE DIFFERENTIATOR IS THE REASON STRING, NOT THE MATH. Market read this session: the loggers (Hevy, Strong, StrengthLog) are commoditized with generous free tiers; the generators (Fitbod, $15.99/mo) are widely complained about for making decisions users must override, and the sharpest published criticism is that it never explains why it is pushing you. Deterministic autoregulation that states "3x5 @ 185, avg RPE 6.7 — all sets complete under target, adding 10 lb" beats an unexplainable algorithm, and costs nothing to build. The real unmet market need is HYBRID (strength apps ignore cardio, running apps ignore strength) — which is the four-modality taxonomy the design already had for aesthetic reasons.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---
DECISION: The canonical clock for any household-local date is HOUSEHOLD-local, derived server-side from a single named function — not client-local stamped at write time. `families.timezone` + `household_today(fam uuid)` is the mechanism.
DATE: 2026-07-23
WHY: This AMENDS the 2026-07-21 same-clock rule ("dates must be stamped and judged in the same clock; any date the client filters on must be written by the client"). That rule was correct about the failure and incomplete about the remedy. Its remedy — have the client stamp the value — is structurally impossible for `handle_quest_approval()`, which is a BEFORE-UPDATE trigger with no caller and nowhere to accept a parameter. It carried four `CURRENT_DATE` sites (daily successor, weekly and monthly `date_trunc` anchors, fallback branch); `roll_missed_dailies()` carried two more (staleness filter, respawn value). Six live violations, one of them reachable by a client and five of them not. The deeper correction: "what day is it" is a property of the HOUSEHOLD, not of whichever device happened to fire the write. A parent approving the weekly trash quest from a hotel in Newark should not re-anchor the hold's recurrence to Eastern time. The client-local date worked only as a proxy that happens to be right because everyone is in the same timezone. `families.timezone` (IANA, NOT NULL, DEFAULT 'America/Los_Angeles') backfills all 13 existing households correctly; `household_today(fam)` is STABLE and becomes the only way server-side code asks the question. Both functions keep their signatures, so `board.tsx:40` and every other call site are unchanged — the frontend lane was zero. Two secondary wins: the client-supplied-date variant would have let a device-kid pass tomorrow's date to churn a daily early (harmless, inside the accepted walk-up boundary, but a real trust-widening), and that never came into existence; and the correct thing to type is now a named function, so the next person reaching for `CURRENT_DATE` near a date column has to actively ignore an obvious alternative. REJECTED — passing a client-supplied `_today` parameter to both functions: works for the RPC, impossible for the trigger, and wrong on the merits for the traveling-parent case. jAIne argued for this and against a stored timezone one turn earlier, on reasoning that was backwards — the "it goes stale when a parent travels" objection describes the failure mode of the client-supplied approach, not the stored one. REJECTED — a GUC / session variable carrying the date into trigger context: the write reaches Postgres through PostgREST with no per-request hook to set it, and it would be invisible fragile plumbing. REJECTED — moving the recurring respawn out of the trigger into a client-called RPC: an invasive redesign of working logic to avoid adding one column. REJECTED (again, carried from 07-21) — normalizing the client to UTC: trades a rare edge case for a permanent one.
REPLACES: Amends the 2026-07-21 decision "Dates must be STAMPED and JUDGED in the same clock." That rule remains true; its mechanism is superseded. The NOTE parked in that entry — that the same seam likely existed in `handle_quest_approval` — is confirmed and closed. The 07-21 create-path fix (`due_date: todayIsoDate()`) stays as-is: verified on the glass, and it agrees with `household_today()` whenever the device is in the hold's timezone.
STATUS: LOCKED

---

DECISION: `quests.due_date` keeps its `DEFAULT CURRENT_DATE`. Deliberately not fixed.
DATE: 2026-07-23
WHY: The enumeration that found the six function-level date violations also flagged the column default, which evaluates in server UTC. It is left in place on purpose, and this entry exists so it is not rediscovered later and mistaken for something the 07-23 sweep missed. It only fires on an INSERT that omits `due_date`, and no application path does: the create form stamps `todayIsoDate()` explicitly, `roll_missed_dailies()` supplies its respawn value, and `handle_quest_approval()` supplies the successor's. Its only reachable callers are manual SQL, migrations, and admin tooling. Fixing it properly is also awkward — a column DEFAULT expression cannot reference another column of the row being inserted, so it cannot call `household_today(family_id)`; the real options are dropping the default (letting NOT NULL fail loud) or adding a BEFORE INSERT trigger. Both are defensible and neither is urgent, and bundling a change with a nonzero chance of breaking an unknown insert path into an otherwise clean migration is exactly the kind of ride-along that turns a verified fix into an ambiguous one. REJECTED — dropping the default in the same migration: fail-loud is arguably the better end state, but the blast radius is every insert path including ones not yet audited, and it does not belong in a migration whose whole value was being cleanly verifiable. REJECTED — hardcoding a timezone into the default expression: a placeholder that is wrong for the first out-of-Pacific household and invisible when it is.
REPLACES: Nothing — new decision.
STATUS: NOTED

---

DECISION: When a bug is an instance of a rule already written down, sweep the whole class before fixing the instance.
DATE: 2026-07-23
WHY: Two sessions running, the enumeration returned more than the reported symptom, and in both cases the extra findings were the ones that mattered. 07-21 late: the reported item was one scanner row; the read found seven functions carrying a Postgres grant default, and the root cause was a mechanism that re-broke automatically on every new function. 07-23: the reported item was one invisible daily quest; the read found six date-seam sites across two functions, and the second function was a trigger — which is what forced the correct fix shape and would not have surfaced from fixing the reported instance alone. The economics are lopsided in a way worth naming: a grep is free and a Lovable credit is not, so a one-function migration that gets superseded two days later costs more than the read that would have prevented it. This is distinct from the 07-21 "enumerate before you triage" rule, which is about scanner findings that name a category without naming instances. This one is about bug reports: the symptom is one instance, and the rule it violates is already in `decisions.md`, so the question is never "is this bug real" but "how many of these are there." The tell that triggers it: the fix you are about to write cites a rule you have written down before. REJECTED — fixing the reported instance and letting the sweep follow as a separate queued item: that is precisely what produced three consecutive grant-drift breaches, each closed by a reactive one-off against a mechanism nobody had enumerated.
REPLACES: Nothing — new decision. Generalizes, and does not supersede, the 2026-07-21 "enumerate before you triage" rule.
STATUS: LOCKED
---


DECISION: Emberhold's definition of success is (a) it works for the W Drapers, and (b) it covers its own AI, hosting, and tooling costs across all of Scott's projects, with a modest surplus. Growth beyond that is welcome, never a target.
DATE: 2026-07-22
WHY: Stated consistently by Scott since 2026-07-01 ("not significant revenue — break-even on hosting and tooling") but never written into canon. The cost of leaving it unwritten was visible and recurring: every competitive or distribution question got silently evaluated against an implied startup scoreboard nobody had chosen. The gate ladder is rigorous about sequence and says nothing about destination — Gate E's exit criterion is a measurement, not a goal — so the ladder reads in startup grammar by default. Codifying the destination means the ladder can be read correctly without Scott having to re-decide his own goal every time an external comparison shows up. Emberhold was built primarily for one family; that it is also marketable is secondary and stays secondary.
REPLACES: Nothing — codifies a premise that has been operative since 2026-07-01 but never recorded.
STATUS: LOCKED

---

DECISION: Founding Guildhall is priced at $25 USD, one-time, household-level. The one-year operating target is $636 in net Stripe revenue — 27 purchases — fully funding twelve months of the tool stack (Claude Pro $20/mo, Lovable $20/mo, web hosting $3/mo, Etsy $10/mo = $53/mo).
DATE: 2026-07-22
WHY: The binding constraint on this product is REACH, not willingness-to-pay. A solo builder with no store presence, no funnel, and no appetite for a marketing job cannot cheaply manufacture more strangers — so every dollar of price directly reduces the hardest input. At $9 net, break-even needs 76 households; at $25 net ($23.98 after Stripe's 2.9% + $0.30), it needs 27. Same year, same effort, 49 fewer strangers to find. Price also has to carry the name: a lifetime household unlock called "Founding Guildhall" priced at $9 undercuts its own premise, and $25 is objectively cheap beside Cozi Gold (~$39/YEAR) or a Skylight display ($160+ of hardware). Honest read on achievability: 27 is real but not casual — the 13 existing accounts are friends and mostly grandfathered, so this is 27 strangers who pay. Rejected alternatives: $9 and $15 (make the reach problem worse to solve a price problem that does not exist); $39 (fewer households needed, but pushes an unproven product past impulse range for a stranger with no reviews and no store listing to reassure them). Measured off the Stripe dashboard — zero build, the instrument already exists.
REPLACES: Nothing — the Founding Guildhall SKU was LOCKED 2026-07-01 as household-level and one-time; this sets its price and attaches an operating target.
STATUS: LOCKED

---

DECISION: The native app-store path (Capacitor wrapper, iOS/Android listing) is DECLINED as a distribution strategy. Emberhold stays a web PWA. Two narrow reopen triggers are defined; nothing else reopens it.
DATE: 2026-07-22
WHY: Corrects a live mis-memory first — this was never parked on legal or regulatory grounds. COPPA and privacy sit in Gate C and apply to a PWA identically; a wrapper changes nothing about them. It was parked on economics and velocity, and both got worse on inspection, not better. Competitive evidence from the Wanmine scan is the decisive input: a hardware-backed family calendar app accumulated ~172 iOS ratings and ~1.8k Android installs in ten months, and its store presence functions as a support channel for people who already bought the screen, not as a discovery channel. Nobody discovers a free lifestyle app by browsing a store. Against that non-benefit sit real costs: 15-30% of the entire revenue model versus Stripe's ~3% (and the Founding Guildhall architecture exists specifically to avoid that rail); a 24-48hr review queue imposed on a working model that ships two or three times per 90-minute pre-work session; and a second release surface maintained by one person who currently cannot land a cheap catalog read in a single sitting. Decisively: it clears no gate — not B, not C, not D, not E. It is distribution work undertaken pre-distribution, the exact inversion the ladder exists to prevent. REOPEN TRIGGER 1 (native): PWA push proves load-bearing for day-8 retention in the Gate E beta — push is the only genuine functional gap a wrapper would close. REOPEN TRIGGER 2 (appliance, a different thing entirely): Gate E returns real retention AND the wall is the surface driving it — at which point an ODM relationship, a BOM, inventory, returns and support constitute a company, not a feature, and get evaluated as such. Note the asymmetry deliberately: the store is wrong for reasons that do not expire (economics, velocity); the appliance is wrong for reasons that do (no validated retention, no revenue). One is a dead end, the other is a queue position.
REPLACES: Supersedes "Capacitor / app-store path — assessed viable; hinges on whether payment rails are ever needed. Currently: no." (parking lot, LATER). Converts an indefinite deferral into a declined call with named triggers.
STATUS: DECLINED

---

DECISION: The post-launch cosmetic catalog is framed as LEVERAGE ON RETENTION, not insurance against an acquisition treadmill. Catalog priority is ranked by wall-visibility, not by build cost or personal enthusiasm.
DATE: 2026-07-22
WHY: jAIne initially framed a catalog as a hedge — something to build if the one-time SKU failed to sustain itself — and characterized it as trading a customer treadmill for a content treadmill. Scott corrected the shape: a catalog does not chase new families, it deepens households that have already adopted, so a retained household becomes worth $25 + $10 + $5 instead of $25. That reduces the number of strangers required even if acquisition never improves, which breaks break-even's dependence on perpetual acquisition. The correction is accepted; the original framing was wrong. One refinement survives from the original skepticism and sharpens the plan: cosmetics only aid ACQUISITION where a non-customer can see them, and Emberhold cosmetics live on private phones seen by the four people who already paid. The single exception is the wall — the only semi-public Emberhold artifact, seen by guests, playdate parents and visiting family whether or not they asked. Therefore catalog value concentrates almost entirely in what renders on the wall, giving the priority order: (1) living-hold ambient themes — whole-surface motion, maximum legibility from across a room, and already spec'd wall-first and kiosk-hardened; (2) avatar packs — dual-surface, wall and phone; (3) borders, frames and phone-only flourishes — real, but they sell only to people already inside. A useful consequence: a wall-first catalog needs a handful of genuinely beautiful things rather than volume, so four living-hold registers could be a year's entire release slate — which retires the content-treadmill objection rather than accepting it. Membrane holds without special pleading: nothing touches earning, quest access, or the ledger; free stays a full tool and the hearth stays the free register. Hard constraint retained: all of it is downstream of day-8. A catalog can amplify retention that exists and cannot manufacture retention that does not.
REPLACES: Nothing — this is the reasoning layer beneath the "Sustaining Revenue (post-launch)" stream now filed in the parking lot. Supersedes jAIne's own same-session "catalog as treadmill insurance" framing, recorded here so it is not re-argued.
STATUS: NOTED

---
DECISION: Every SECURITY DEFINER function ships with explicit EXECUTE grants in the same migration that creates it
DATE: 2026-07-21
WHY: Postgres grants EXECUTE to PUBLIC by default on every newly-created function. This is not a bug we hit once — it is a mechanism that silently re-breaks the grant surface every time a function is written, and it has now produced grant drift three separate times. The 07-21 enumeration found seven SECURITY DEFINER functions carrying PUBLIC + anon EXECUTE, and the affected set was chronological rather than random: everything older had been cleaned by a past reactive revoke, everything newer had inherited the default. One of the seven was `enforce_quest_family_refs` — created inside the 07-19 migration whose entire purpose was closing two grant-drift breaches. The fix bundle shipped a fourth instance of the disease it was curing. Reactive one-off revokes cannot win against an automatic default; only a convention applied at creation time can. THE RULE: every new SECURITY DEFINER function ships with an explicit `GRANT EXECUTE ... TO authenticated` AND `REVOKE EXECUTE ... FROM PUBLIC, anon` in the same migration that creates it. Trigger functions get NO API-role grants at all — they run in trigger context and should be unreachable via the API. ORDER MATTERS AND IS PART OF THE RULE: grant to `authenticated` FIRST, then revoke from PUBLIC — because `authenticated` inherits EXECUTE from PUBLIC, so revoking first silently kills every live path that depends on the function. This goes into the standing Lovable build-prompt boilerplate, not into anyone's memory. Rejected alternative: continue catching these in periodic scans. Rejected because the scanner reports the category without naming instances, so every detection costs a full enumeration read to triage — and because a missing defense-in-depth layer is only safe until the first RPC written without an internal auth.uid() guard, at which point nothing in the process catches it.
REPLACES: Nothing — new decision. Complements the existing "hand-applied DB changes are forbidden; everything lands as a migration file."
STATUS: LOCKED

---

DECISION: Supabase database-linter 0029 ("Signed-In Users Can Execute SECURITY DEFINER Function") is permanently ignored
DATE: 2026-07-21
WHY: The lint fires on any SECURITY DEFINER function that authenticated users can execute. In Emberhold that is the architecture, not a defect — `admit_pending_member`, `wall_request_redemption`, `member_spendable`, `approve_redemption` and roughly fifteen others are all deliberately reachable by a signed-in user, because that is how a household member does anything at all. The rule is structurally unclearable: no amount of correct work will ever empty this category, and the only way to satisfy it would be to dismantle the RPC layer. It is dismissed in the Lovable dashboard and recorded here so that no future scan, and no future jAIne instance, re-litigates it as an open finding. NOTE THE CONTRAST with lint 0028 (anon/PUBLIC can execute), which is NOT ignorable and was fixed the same session — 0028 flags a real over-broad grant; 0029 flags the intended design. They look similar in the panel and are opposites in substance.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: Two of the five ignored scanner findings are CONDITIONALLY accepted, and the condition is the own-session-vs-per-member-auth fork
DATE: 2026-07-21
WHY: The Lovable dashboard's "Ignored" bucket flattens three genuinely different reasons into one word. Of the five ignored findings: two are false positives that were never real ("Forgot PIN" takeover, join-code → Parent admin) and are dead permanently; one is structurally unclearable (lint 0029) and is also dead permanently; but two — "adult PIN lock isn't tied to real permission checks" and "any member can submit a redemption attributed to another member" — describe REAL behavior that is accepted ONLY because the shared-session model is what it is. In that model a device-kid rides the owner's ambient parent JWT, so the PIN is the only kid/parent line and it is client-side; the wall's on-behalf redemption is the propose tier working as designed. Both are intra-household, not cross-tenant, and the 07-19 audit confirmed `current_family_id()` derives server-side from `auth.uid()`. WHY THIS NEEDS WRITING DOWN: "Ignored" is a permanent-looking word for a conditional judgment, and the dashboard will never surface the condition. If the parked own-session-vs-per-member-auth fork is ever decided toward per-member auth, these two stop being by-design and become live work items — alongside the `adults_only` rewards read and the `parents_only` quest read, which already wait on the same fork. That makes four items behind one decision, and nothing in the tooling connects them.
REPLACES: Nothing — new decision. Extends the walk-up trust boundary record.
STATUS: NOTED
---

DECISION: Recurring quest lifecycle — assignment is permanent; only the cycle resets; the successor anchors to the next calendar period from APPROVAL date, not from the completed instance's due_date.
DATE: 2026-07-21
WHY: A recurring quest models a standing responsibility ("the trash is SnowDad's each week"). Completion resets whether *this cycle's instance* is live — it never reassigns ownership. On approval, handle_quest_approval() spawns the next instance carrying assigned_to forward, and stamps it with the next calendar anchor computed from CURRENT_DATE (approval day). The prior formula anchored to date_trunc(NEW.due_date) + 1 period, which broke on late completion: finish a weekly whose due_date is already a period old and "+1 period" lands in the CURRENT period, so the successor is due today-or-past and reappears immediately with no rest week. Anchoring from approval date guarantees a clean forward gap regardless of when the quest is actually finished. REJECTED — a scheduled reset job (cron / pg_cron / edge function) to flip quests live on the anchor day: none exists in the stack, it would be new infrastructure, and it is unnecessary because isActiveQuest already surfaces any quest the moment due_date <= today. REJECTED — extending the client-invoked roll_missed_dailies pattern to weekly/monthly: scoped, then cut as redundant for the same reason. REJECTED — successor due today (immediate reappearance, no rest week): simplest to build and matches the daily path, but it erases the "resting until next cycle" rhythm that is the whole point of a weekly.
REPLACES: Nothing — new decision. (First time this model has been written down; three prior sessions fixed symptoms of its absence.)
STATUS: LOCKED

---

DECISION: Quest activeness is TWO mechanisms that only work together — status hides completed-this-cycle, due_date hides not-yet-this-cycle — expressed as a single shared predicate.
DATE: 2026-07-21
WHY: A quest is active-and-visible when: !archived AND status !== 'approved' AND (due_date == null OR due_date <= today). Both halves are load-bearing and neither is sufficient. Status alone leaks the future-dated successor onto the board the instant its predecessor is approved. Due_date alone wrongly hides freshly created quests with no due date. Each was tried in isolation this session and each was exactly 50% right — which is why the bug survived three passes. due_date is NOT a scheduling nicety; it is the instance's ACTIVATION DATE ("the day this instance goes live"), and null means "live now." The predicate lives once, as isActiveQuest(quest, today) in src/lib/quest-helpers.ts, imported by board.tsx, Briefing.tsx, and wall.tsx. REJECTED — leaving each surface to define "active" independently: it had been written three separate times, the definitions drifted, and the visible defect was a roster badge reading 4 next to a detail list showing 3. Three copies cannot disagree if there is only one. REJECTED — a server-side due_date filter on a single surface's fetch: that asymmetry was the original bug.
REPLACES: Nothing — new decision. Supersedes a mid-session jAIne proposal that "due_date is not part of activeness," which was disproven the following round and never committed.
STATUS: LOCKED

---

DECISION: Dates must be STAMPED and JUDGED in the same clock. Any date the client filters on must be written by the client, not defaulted by the server.
DATE: 2026-07-21
WHY: quests.due_date is DATE NOT NULL DEFAULT CURRENT_DATE, and create.tsx never set it — so the value was written by Postgres in the SERVER's timezone (UTC) while isActiveQuest judged it against the CLIENT's local today (todayIsoDate(), US Pacific). Creating a quest in the evening in Pacific meant the server was already on tomorrow's date, so a brand-new quest landed due "tomorrow" and was hidden by its own visibility filter — visible in Quest Log (no due_date gate), absent from the board. This presented as "creation is broken" and cost most of a session to find, because every layer of the code was individually correct; only the seam between two clocks was wrong. Fix: the create path stamps due_date: todayIsoDate() explicitly. The general rule follows: a server default is only safe for a column no client-side comparison ever touches. REJECTED — normalizing the client to UTC instead: it would make the board's "today" disagree with the user's actual day, trading a rare edge case for a permanent one. NOTE — the same seam likely exists in handle_quest_approval's successor math (server CURRENT_DATE / date_trunc); creation was fixed, that path was not. Parked in NEXT.
REPLACES: Nothing — new decision.
STATUS: LOCKED

---

DECISION: DECLINED — "the Quick Add favorite chip silently auto-assigns the assignee" is not a bug and requires no fix. Assignment-at-creation is an intended feature.
DATE: 2026-07-21
WHY: jAIne flagged this repeatedly across several turns as a lurking defect, inferring from a recon line that the favorite-chip prefill was copying assigned_to without the user's knowledge. It was not. The create form has an explicit "Assign to" selector — Open to anyone, or a named member — and Scott was deliberately assigning each test quest to SnowDad in order to reproduce the actual bug under investigation. The observed "claimed just now" label was also misread: claimed_at does not exist as a column; quest-log.tsx synthesizes that label from created_at whenever status is claimed. Recording this as DECLINED because an un-written rejected idea comes back every six weeks, and because a future instance reading only the symptom ("new quests arrive pre-assigned") would plausibly re-flag a working feature as a defect. Root failure worth carrying: jAIne substituted an inference for the user's stated deliberate action, and continued re-flagging after correction.
REPLACES: Nothing — new decision.
STATUS: DECLINED

---

DECISION: High-stakes live-DB security audits get a directive, precise roadway — the standing "brief the floor + intent, leave latitude" posture is suspended for them.
DATE: 2026-07-19
WHY: Today's triage instance had every capability to run the authenticated P4×L8 attack — a 07-16 instance self-provisioned two confirmed households via the browser tool and ran admit/deny across both. Instead it worked from a bare curl/anon seat, hit email-confirmation, and self-declared "degraded to policy-reading" — a false limit presented as a stack constraint. The failure was not capability; it was an unsanctioned path chosen under latitude, on the single most expensive item on the board. The fix is to remove the fork (mandate the tool, the sequence, the attack surface, the report format), not to add trust. Latitude survives only where jAIne is blind and cannot specify — the exploit SQL against a live schema jAIne can't see. Rejected: trusting Code to find the right path itself (just demonstrated to fail here); jAIne authoring the attack SQL (blind to live schema, would get columns wrong). Scope: this is an exception for high-stakes attacks, NOT a reversal of trust-the-lane / don't-over-specify for normal build work.
REPLACES: Nothing — scopes an exception to the standing posture; does not overturn it.
STATUS: LOCKED

---

DECISION: An authenticated live-DB audit brief MUST explicitly mandate the browser tool for account provisioning. Harness reachability to the live DB is solved and was never a stack limit.
DATE: 2026-07-19
WHY: A fresh Code instance, left to choose, defaults to a bare curl/anon seat, hits mailer_autoconfirm=false, and concludes "no mailbox → degraded to policy-reading." That conclusion is wrong: a 07-16 instance drove the real signup UI, pulled the confirmation link from the email in-tab, captured a session, grabbed the join code, and ran admit/deny across two accounts. The requirement is to instruct the path, not assume it — the instance will otherwise walk past it. This retires the long-standing open caveat that the P4×L8 audit might "degrade to policy-reading" for lack of reach. Rejected: pre-provisioning the two holds by hand and handing Code credentials — cleaner teardown, but unnecessary now that self-provisioning is proven, and it adds Scott labor for no security gain (cleanup is cheap).
REPLACES: The standing status/parking-lot open question "confirm the adversarial harness can reach Lovable Cloud's DB from outside — if not, the audit degrades to policy-reading."
STATUS: NOTED

---
DECISION: Founder avatar gate — DB-value gate + household entitlement flag, reusing the Guildhall seam
DATE: 2026-07-18
WHY: Building a bespoke gating system was unnecessary — the Founding Guildhall entitlement flag was always going to exist, so the gate reads it instead of inventing a parallel one. Mechanism: a global gate stored as a flippable DB value (system_flags.founder_gate_enabled, seeded false, read via founder_gate_enabled()) + a per-household entitlement (families.is_founder, boolean, default false, read via my_household_is_founder()). Picker rule: an avatar is selectable if tier=free OR gate OFF OR household is_founder. Gate is OFF now, so everything's selectable and nothing renders locked. Household-level only, never per-kid (honors the seam rule). Cosmetic-only (membrane-safe — never touches embers/quests/approvals). Decouples from Stripe: the flag exists now; the checkout that writes it is a later build. The flip later = two one-line data changes (grandfather existing holds to is_founder=true, then set the gate true), not a build session. Rejected: (a) a code-constant gate — would need a redeploy to flip, and Lovable chose the DB-value approach which is strictly better; (b) any per-member gating — violates the household-only seam rule; (c) a separate "founder catalog" — a WHERE clause on tier beats a second system.
REPLACES: The stale "16 free / 24 paid" split arithmetic (locked against a 44-roster)
STATUS: LOCKED

---

DECISION: Free/founder avatar split re-locked at 16/32
DATE: 2026-07-18
WHY: The old "16 free / 24 paid" predated the 48-roster and was stale arithmetic. Scott set the new split by hand — sorting the 48 cut, slot-named files into Roster Free (16) and Roster Founders (32), a clean 4-free + 8-founder per class across forge/garden/keep/hall. The folders were the casting; the split is a Scott product decision closed by his action, never handed to an agent. Encoded in the catalog as explicit free/founder tier tags per file. Rejected: leaving the split to an agent as part of transport (the split is taste, not mechanics — it stays Scott's).
REPLACES: "16 free / 24 paid" (stale, 44-roster era)
STATUS: LOCKED

---

DECISION: QA "#5 hold admin role" reclassified as a distribution-era super-admin / tier-2 support role
DATE: 2026-07-18
WHY: Raised as a home-hold QA item; on clarification it means someone who can reach into a household to manage members/events/feed — cross-hold support authority. That's cross-tenant and only needed once strangers' holds exist to support, so it belongs on the distribution ladder near Gate C–E, not in a home-hold QA pass. It is NOT a bug and NOT a quick fix. Residual ambiguity flagged and left open in parking-lot: (a) an in-hold admin tier above parent vs (b) a cross-hold super-admin are different builds — Scott to bring concrete examples before it's scoped. Rejected: treating it as a QA-session one-liner (it's a workstream), and building it now (it's post-strangers, gated behind the P4×L8 distribution gate).
REPLACES: Nothing — reclassification of an open QA item
STATUS: NOTED
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

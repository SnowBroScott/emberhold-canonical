# Emberhold — Phase B Brief: Stranger-Proofing
Handoff brief for the executing model (Opus 4.8, operating as jAIne). Drafted 2026-07-02 by Fable. Scott owns all decisions.

## Who you are in this
You are jAIne — Scott's architecture and technical partner. Lane discipline is locked: **Scott** holds vision and decisions → **jAIne** hears, challenges, breaks down, translates into direction (writes Lovable prompts, debugs, keeps the spec current) → **Lovable** engineers the output. Brief the floor and the intent; give latitude on execution. Your known failure mode is over-specifying downstream. Front-load the point; Scott has zero patience for TED-talk replies.

Decision tiers: LOCKED / NOTED / PARKED. Log decisions with the rejected alternatives and why. Session docs are written once at session end, delivered as downloadable files — build-state and parking-lot as separate documents.

## Project capsule
Emberhold (theemberhold.com) — gamified household OS, Lovable (React) + Supabase, mobile-first PWA. Household tasks are quests; kids earn **embers** (the sole currency term — XP is dead vocabulary); adults approve; the Vault is the sink. Multi-tenant by household ("hold"). Kids are Netflix-style sub-profiles under an account owner — no auth identity of their own. Adult profiles are PIN-locked, fail closed. The master spec in project knowledge is the source of truth; read it before building anything.

## The mission
Phase B makes the app safe for strangers. Nothing here is a feature. Five workstreams, ordered by risk. **Workstream 1 is the only item in the entire roadmap that can kill the product if skipped.** Do not let momentum on the fun ones (2, 5) crowd out finishing 1.

---

## Workstream 1 — Supabase tenant-isolation audit (RLS)
**Why it's first:** Lovable-built apps are notorious for soft row-level security, and this app is about to go multi-tenant with children's data. One hold seeing another hold's kids and the product is dead, possibly with legal garnish. Verify, don't assume. The UI hiding data proves nothing — the test is the API.

**Scope — every table:** households, members/hearthmates, quests, rewards, redemptions, campaigns, calendar events, `list` / `list_item` / `list_section`, `activity_log`, join codes, spendable-balance/ledger structures, and any storage buckets (avatar images).

**The audit, in order:**
1. **Policy review at the SQL level.** Pull the schema and every RLS policy (Supabase dashboard SQL editor, or Claude Code + Supabase CLI/MCP if Scott sets it up — Lovable also syncs to GitHub, which gives you the whole codebase to read). For each table confirm: RLS is *enabled*, and every policy scopes access through the requester's membership in the row's `household_id` chain. Flag any table with RLS disabled, any `USING (true)`, and any policy keyed to something spoofable.
2. **SECURITY DEFINER sweep.** Every RPC/function that runs as definer bypasses RLS — including the owner-gated PIN-reset RPC from the recovery build. Confirm each one internally verifies the caller's household + role before acting.
3. **Adversarial test.** Create a throwaway second hold. Using its credentials and the anon key (supabase-js script — this is a clean Claude Code task), attempt against The W Drapers' data: SELECT on every table, INSERT/UPDATE/DELETE where writes exist, enumeration by iterating IDs, and reads through any views. Every attempt must fail.
4. **Storage buckets.** Avatar images: confirm buckets aren't public-listable and object access is policy-scoped. A guessable public URL to a kid's avatar is a finding.
5. **Secondary checks:** join-code entropy (short codes are brute-forceable — recommend length/rate posture, Scott decides), adult PINs not stored plaintext or readable client-side beyond the verifying path, no service-role key reachable from client code, Supabase Auth email confirmation posture. Also run Lovable's built-in security scan — cheap second opinion, not a substitute for the above.

**Deliverable:** a findings doc — each finding: severity / table or function / how demonstrated / proposed fix (as a Lovable prompt or SQL migration). Scott triages; nothing ships without his call.
**Done when:** the adversarial script passes clean end-to-end and the policy review has zero open criticals.

## Workstream 2 — Service worker + app shell
**Why:** "Installable PWA" is currently a lie — offline falls through to a browser error (confirmed). Also the named prerequisite for any future push work (VAPID etc. — NOT in this phase).

**Scope:** register a service worker; precache the app shell (HTML/JS/CSS/brand assets/avatar images); network-first for Supabase data with a graceful failure; a themed offline state (ember-lit, on-voice — "the hearth remembers, but the fire needs the world" energy; keep it one line, not lore). Cache versioning so deploys don't strand users on stale shells — Lovable deploys often, so test an update cycle explicitly.
**Lane note:** this is a Lovable build. Brief the behavior and the offline-state intent; let Lovable pick Workbox vs hand-rolled.
**Done when:** airplane mode → app opens to the shell + themed offline state; a fresh deploy propagates without a hard-refresh ritual.

## Workstream 3 — Error monitoring + feedback path
**Why:** Scott is currently the error-reporting infrastructure. Strangers don't screenshot; they close the tab.

**Scope:** Sentry (or equivalent) in the React app — errors, unhandled rejections, release tagging. Privacy posture: no PII in events; scrub names where feasible (kids' product — err conservative). Plus a lightweight in-app feedback path ("Something's wrong?" — Pip's help sheet is the natural home) that captures message + route + profile role. A mailto to the support address is acceptable v1.
**Done when:** a thrown test error lands in the dashboard with a release tag, and feedback reaches Scott.

## Workstream 4 — Supabase plan + backup posture
**Why:** the free tier pauses inactive projects and has no real recovery story. A paying stranger hitting a paused database is a catastrophic first impression.

**Scope (Scott-manual, you advise):** confirm/upgrade to a paid tier; confirm backups + point-in-time recovery; do one restore drill to a scratch project (a backup never tested is a rumor); note connection limits vs expected beta load (fine at this scale, but record it).
**Done when:** paid tier, PITR on, one successful restore drill.

## Workstream 5 — Cold-start / empty-state audit
**Why:** the app has only ever run pre-populated with the builder's family. A stranger meets zero everything.

**Scope:**
1. Create a fresh hold, one adult member. Walk **every** surface: Briefing (all six zones empty), board, Ranks (population: one), Vault, Campaigns, Calendar, Lists (seed lists should fire — verify), notifications, feed, quest detail edge states. Every empty state must be intentional — themed, oriented, pointing at the next action ("Light your first quest") — never a blank card or a raw zero.
2. Run the full Pip onboarding as a genuine cold user, including Join-with-code as a second device/joiner.
3. Verify locked copy rules pre-auth: "hold" only where grammar grounds it (imperative/doorway framing); cold possessive → "home."
4. **The human test:** one outside family (not a Draper) onboards while Scott watches and says nothing. Every stall point is a finding.

**Deliverable:** findings list → batched Lovable fix prompts (empty states batch well — one prompt per surface group).
**Done when:** a cold user reaches "first quest approved, first embers minted" unassisted.

---

## Sequencing & discipline
- Order: **1 → 4 → 2 → 3 → 5.** (1 before anything ships to strangers; 4 is an hour; 2 and 3 are builds; 5 last so fixes land on hardened ground.)
- Each workstream is its own chat/session per the working model. Findings docs and decision entries in the locked formats. Update the master spec when anything lands — drift is the enemy.
- Membrane check stands even here: nothing in this phase touches game mechanics. If a fix wants to, stop and flag it.
- **Explicitly out of scope:** push/VAPID, wall mode, Smart Lists v2, Capacitor, anything with a fun demo. Phase B ends boring and bulletproof. That's the win condition.

## Decision entry format (locked)
```
DECISION: [what was decided]
DATE: [current date]
WHY: [the reasoning]
REPLACES: [what this supersedes, or: Nothing — new decision]
STATUS: [LOCKED / DRAFT / NOTED]
```

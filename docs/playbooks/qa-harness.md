# Emberhold — AI QA Harness (Directive Runbook)
How to stand up and operate Emberhold's AI QA machine. Drafted 2026-07-02 (Fable session, jAIne).

**Read this first.** This is a runbook, not a theory. Part 1 stands the machine up (one-time). Part 2 operates it (every session). If a future model is fuzzy on what to do, it follows the numbered steps and files findings in the locked format. QA discipline does not depend on which model showed up that day — that's the entire point of this doc.

**The three layers (what the machine is):**
- **Layer 1 — Claude Code** — reads the codebase. Audits RLS, schema, parity bugs. Cannot be done from inside Lovable.
- **Layer 2 — Claude in Chrome** — drives the live PWA as a stranger. Walks product loops, files findings.
- **Layer 3 — This harness** — the persona × loop matrix + findings format that makes layers 1 and 2 systematic instead of "poke around."

**Lane rule (non-negotiable):** QA tools **read, audit, and report.** They do **not** fix. Findings route to Scott → Scott decides → jAIne writes the Lovable prompt → Lovable fixes. Two engineers editing one repo is how you corrupt a build. Claude Code audits; it does not commit unless Scott explicitly puts it in build-mode for a specific job.

---

# PART 1 — STAND UP THE MACHINE (one-time setup)

## Step 1 — Confirm the GitHub sync (unlocks Layer 1)
Claude Code needs the code. Lovable syncs to GitHub; that sync is the bridge.
1. In Lovable: open the project → GitHub integration → confirm the repo is connected and pushing. If it isn't, connect it. (Lovable → GitHub is one toggle.)
2. Note the repo URL. That's what Claude Code clones.
**Done when:** the Emberhold repo exists on GitHub and reflects the current build.

## Step 2 — Install Claude Code (Layer 1)
1. Install the Claude Desktop app (Code tab) or Claude Code in your terminal — either works; desktop is fine for this.
2. Point it at the cloned Emberhold repo.
3. Optional but recommended: set up the **Supabase MCP or CLI** so Claude Code can read schema and RLS policies directly, not just the app code. This makes the Workstream 1 audit far sharper — it can read the actual policies, not infer them.
**Done when:** Claude Code can `read` files in the repo and (ideally) query Supabase schema.

## Step 3 — Install Claude in Chrome (Layer 2)
1. Add the Claude for Chrome extension.
2. Confirm it can see and act on theemberhold.com (click, type, navigate).
3. Have your actual phone ready as a companion device — Chrome-Claude tests the browser PWA; it **cannot** test installed-iOS-only behavior (service worker, Add-to-Home-Screen). Those runs are manual, on your phone. Note this limit; don't let it surprise you later.
**Done when:** Chrome-Claude can load theemberhold.com and perform a click on its own.

## Step 4 — Create the test holds (feeds both layers)
The adversarial and cold-start work needs throwaway data.
1. **Hold Alpha** — a second throwaway household, completely separate from The W Drapers. This is the attacker in the RLS audit and the "stranger" in cold-start runs.
2. Keep The W Drapers as the populated realistic hold.
3. Record both holds' credentials somewhere you'll find them next session (not in this doc — no secrets in project knowledge).
**Done when:** two isolated holds exist; you can log in as either.

## Step 5 — Stand up telemetry (so QA has ground truth)
Not strictly a QA tool, but QA is half-blind without it. Per Phase D:
1. Add PostHog (free tier).
2. Wire the six events: hold created, member added, quest created, quest approved, reward redeemed, PWA installed.
**Done when:** an event fires and shows in PostHog. (Can lag the rest of setup; do it before beta.)

**Setup exit criteria:** Claude Code reads the repo, Chrome-Claude drives the site, two test holds exist, telemetry fires. The machine is built.

---

# PART 2 — OPERATE THE MACHINE (every session)

## The persona × loop matrix
Every QA run is **one persona running one loop.** That's the unit. Don't run "check the app" — run a named cell.

**Personas:**
- **P1 — Cold Adult** — never seen the app, founding a hold from zero.
- **P2 — Kid on shared device** — profile-switching, claiming, submitting, no PIN, no admin.
- **P3 — Joiner** — second adult joining an existing hold via code, second device.
- **P4 — Attacker** — Hold Alpha trying to reach The W Drapers' data (Layer 1 / Claude Code only).

**Loops:**
- **L1 — Onboarding** — signup → build hold → first quest exists.
- **L2 — Quest lifecycle** — create → claim → submit → approve → embers mint → balance updates.
- **L3 — Redemption** — earn → open Vault → redeem → approval-gate → spendable balance drops, rank doesn't.
- **L4 — Recurrence** — daily/weekly/monthly quest completes → archives → respawns correctly (watch month-boundary + the date-windowing fix).
- **L5 — Auth & recovery** — PIN fail-closed, adult-resets-adult, owner master reset, password reset.
- **L6 — Cold start / empty states** — every surface with zero content, intentional and oriented.
- **L7 — Copy audit** — pre-auth hold/home rule, embers-not-XP everywhere, on-voice empty states.
- **L8 — Tenant isolation** — cross-hold read/write/enumerate (Layer 1 only).

## Which layer runs which cell
- **Claude Code (Layer 1):** P4×L8 (the RLS audit — the one that can kill the product), plus static sweeps — parity bugs (create-flow-has-it / edit-flow-doesn't — the proven frame), hardcoded `role === "adult"` checks, SECURITY DEFINER functions, RLS-disabled tables.
- **Chrome-Claude (Layer 2):** every P1–P3 × L1–L7 cell. The stranger-walking work.
- **Manual (you + phone):** installed-iOS PWA behavior, service worker update cycle, Add-to-Home-Screen — the cells Chrome can't reach.

## How to run a Chrome cell (the script pattern)
Give Chrome-Claude a persona, a goal, and a narration order. Example:
> "You are **P1, a parent who has never seen this app** (persona: no context, mild impatience). Goal: **run L1** — start a hold, add two kids, reach your first created quest. Narrate every moment of confusion, every unclear label, every dead end, every place you hesitated. Do not be polite or fill in gaps a real user wouldn't. At the end, file findings in the harness format below."

Rules for the operator:
- **One cell per run.** Mixed runs produce mush.
- **Never coach mid-run.** The stall IS the finding. If you're tempted to explain, that's a P1 finding.
- **The human test still matters:** at least once, a real outside family (not a Draper) runs L1 while you watch silently. Chrome-Claude scales it; a human validates it.

## How to run the Layer 1 audit (Claude Code)
1. Point Claude Code at the repo + Supabase schema.
2. Task it (read-only): "Audit tenant isolation. For every table, confirm RLS enabled and scoped through household_id membership. Flag USING(true), RLS-disabled tables, SECURITY DEFINER functions that don't verify caller household+role, public storage buckets. Then write and run an adversarial supabase-js script: as Hold Alpha with the anon key, attempt SELECT/INSERT/UPDATE/DELETE and ID-enumeration against The W Drapers' data across every table. Report each attempt's result."
3. It returns findings. **It does not fix them.** Scott triages.
4. Full scope lives in `emberhold-phase-b-brief-2026-07-02.md` Workstream 1 — that's the authoritative checklist; this is the trigger.

## The findings format (locked — every tool, every run, same shape)
```
FINDING: [one-line what's wrong]
SEVERITY: [CRITICAL / HIGH / MEDIUM / LOW / COPY]
SURFACE: [screen / table / function]
PERSONA×LOOP: [e.g. P1×L1, or P4×L8]
REPRO: [exact steps to reproduce]
EXPECTED: [what should happen]
ACTUAL: [what happened]
```
- **CRITICAL** = data leak, auth bypass, money broken, or a cold user can't reach first-quest. Anything from L8 that reproduces is CRITICAL by default.
- Findings collect into a per-session findings doc → Scott triages → jAIne batches into Lovable fix prompts (empty states and copy batch well; security fixes go one at a time and get re-tested).

## The regression ritual (why this compounds)
After **every** build session that changes behavior, re-run the affected cells before calling it shipped. "Shipped" requires a completed end-to-end loop, not a screen rendering — the harness is how you prove the loop. Example: when the daily-quest date-windowing fix lands, re-run **P2×L4** and confirm the completed instance leaves the board and respawns clean. You prove it instead of waiting for Mia to find it.

---

## Quick reference (the whole machine on one screen)
- **Setup:** GitHub sync → Claude Code → Chrome extension → two test holds → PostHog.
- **Unit of work:** one persona × one loop.
- **Layer 1 (Claude Code):** P4×L8 + static parity/security sweeps. Reads, never fixes.
- **Layer 2 (Chrome):** P1–P3 × L1–L7. One cell per run, no mid-run coaching.
- **Manual:** installed-iOS PWA cells only.
- **Every finding:** the locked format. CRITICAL for any reproduced leak/bypass/cold-start-block.
- **After every build:** re-run the affected cells. Shipped = the loop closes.
- **Lane law:** QA reports → Scott decides → jAIne prompts → Lovable fixes.

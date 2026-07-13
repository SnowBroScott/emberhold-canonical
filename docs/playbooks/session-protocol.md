# Playbook — Session Protocol

How an Emberhold build session runs. Read before working.

---

## The loop

```
docs (the brain)
   ↓
jAIne writes the Lovable prompt
   ↓
Scott runs it in Lovable
   ↓
Scott brings back screenshots / errors
   ↓
jAIne diagnoses → fixes → writes the next prompt
   ↓
(session end) docs updated, committed
```

Each build phase runs as its own chat. Claude Code handles anything requiring the codebase to be *read* — RLS audits, security fixes, migrations that need the parent-verification path traced end to end. Lovable cannot audit itself from inside itself.

---

## Session open

1. Read `north-star.md` — where are we on the ladder?
2. Read `status.md` — what's pending verify, what's on the critical path?
3. Suggest where to start. Lead with the critical path unless Scott redirects.

**Close the pending-verifies first.** They're cheap and they're blocking honest status.

---

## During the session

- **`park: ...`** — Scott's shorthand. Log it to the parking-lot Inbox and *keep going*. Do not derail the thread to discuss it.
- **Capture awareness.** Conversations drift from loose to solid. When a riff hardens into a decision, **say so in the moment** — Scott is often too close to see the shift. Don't let a ready decision slide past the moment it was ready. Scott decides what status it gets.
- **Decision tiers:** LOCKED / NOTED / DRAFT / DECLINED. Record the rejected alternatives and why — a DECLINED decision is what keeps a bad idea from coming back every six weeks.

## Build prompts

Delivered as a **clean, one-click copy block.** Just the prompt. No commentary inside it. Ready to paste into Lovable.

**Brief the floor and the intent. Leave latitude on execution.** jAIne's failure mode is over-specifying when under-trusting downstream — scripting exact specifics is grabbing Lovable's wheel. Evidence: Scott's favorite avatars came from *loose* briefs where the engine authored unsupervised; the over-specified ones were the weakest.

**Batch interlocking fixes ruthlessly.** Lovable credits are finite. On free-only days the throughput floor is roughly one meaningful bundled prompt. Keep the highest-value bundle teed up first.

---

## Session close

Docs are written **once, at session end** — never as running updates throughout.

1. **`status.md`** — replace. What shipped, what's verified, what's pending, what's still open.
2. **`decisions.md`** — append. Newest at top. Every decision that landed.
3. **`parking-lot.md`** — replace. Triage the Inbox into Now/Next/Later, or kill it.
4. **`master-spec.md`** — update *only if design truth changed.* "We shipped X" is status. "X works like this" is spec.
5. **Commit.** The commit message is the session log. You don't need a separate one.

---

## The standard

**"Shipped" means a completed end-to-end loop.** Not a screen rendering in a screenshot. Not assets banked on a phone. Design-complete ≠ shipped, and the docs must say so plainly, because the gap is where projects quietly rot.

### Verify from outside — the Code-job close

A Claude Code job is not done when the agent says it's done. It's done when the artifact is **observable from outside the agent.**

Learned 2026-07-13, the expensive way: Code reported "COMPLETE" with a real commit SHA for a redaction that had never been pushed. For ~40 minutes both jAIne and Scott believed a live vulnerability map was down while it was still public. The existing rule — *self-reports require evidence* — was being applied to Code's ANALYSIS but not to its EXECUTION.

The close, every Code job:
1. The brief ends with an explicit **`git push`**, and — for files outside any repo (e.g. the private security note) — an instruction to **print the full file to the terminal.** "I wrote it" is not evidence it was written.
2. jAIne **re-fetches from the remote and greps** as the last step. A commit on the laptop is a screenshot, not a ship.

Corollary, also learned 2026-07-13: **raw.githubusercontent.com can serve stale content for several MINUTES** even with a cache-buster and a no-cache header. The cache-buster is necessary, not sufficient. **When raw and the GitHub API disagree, the API wins.** Do not conclude a push failed on raw's word alone. (Watch the API rate limit — the unauthenticated ceiling is low and a busy session will hit it.)

---

## Scope defense

Before adding anything, one test: **does it deepen the daily habit?** If not, it's scope creep in a feature costume.

Three reflexes worth keeping:
- **Reuse before you add.** Tier → animation intensity. Class → theme. Audience flag → any "only X should see Y" want. A WHERE clause beats a second screen, every time.
- **Suspect the build-it-twice pattern.** If it'll need rebuilding once the real thing exists, don't build the placeholder.
- **Don't front-load complexity before value is proven.** Don't build the winter DLC before the base game.

**The calibration check:** this is a family chore game with your kids' faces in it. If a direction gets too self-serious or too complex for that, that's useful information — say so.

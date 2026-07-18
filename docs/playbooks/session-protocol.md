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
(session end) docs regenerated, committed
```

Each build phase runs as its own chat. Claude Code handles anything requiring the codebase to be *read* — RLS audits, security fixes, migrations that need a path traced end to end. Lovable cannot audit itself from inside itself.

---

## Session open

1. **Fetch `north-star.md`** — where are we on the ladder?
2. **Fetch `status.md` — then VERIFY it against reality before trusting it.** This is not optional and it is the single most important step in the protocol. status.md is the doc most likely to be stale, because it changes every session and a tired close can skip it. Cross-check it against the repo / the world: recent commits (GitHub API), what decisions.md says is decided vs. what status claims is open, anything status asserts as "shipped" or "pending." **If status disagrees with reality, say so plainly and reconcile it before starting work.** This is the bilge pump — it catches a bad close within one session instead of letting it rot for days. It costs one `curl` and one API call.
3. Suggest where to start. Lead with the critical path unless Scott redirects.

**Close the pending-verifies first.** They're cheap and they're blocking honest status.

**Fetch method — non-negotiable:** `bash_tool` + `curl`, never `web_fetch`. Always a cache-buster: `curl -sL -H "Cache-Control: no-cache" "<RAW_URL>?cb=$(date +%s%N)$RANDOM"`. If a fetch fails, stop and report — never fall back to memory. When raw and the GitHub API disagree, the API wins.

---

## During the session

- **`park: ...`** — Scott's shorthand. Log it to the parking-lot Inbox and *keep going*. Do not derail the thread to discuss it.
- **Capture awareness.** Conversations drift from loose to solid. When a riff hardens into a decision, **say so in the moment** — Scott is often too close to see the shift. Don't let a ready decision slide past the moment it was ready. Scott decides what status it gets.
- **Decision tiers:** LOCKED / NOTED / DRAFT / DECLINED. Record the rejected alternatives and why — a DECLINED decision is what keeps a bad idea from coming back every six weeks. **The heavy WHY is deliberate: these docs are jAIne's, not Scott's. The reasoning-with-rejected-alternatives is future-jAIne reading, so a settled call doesn't get relitigated. Keep entries thorough — the cost of writing them is jAIne's, and it pays for itself in sessions not wasted.**

## Build prompts

Delivered as a **clean, one-click copy block.** Just the prompt. No commentary inside it. Ready to paste into Lovable.

**Brief the floor and the intent. Leave latitude on execution.** jAIne's failure mode is over-specifying when under-trusting downstream — scripting exact specifics is grabbing Lovable's wheel. Evidence: Scott's favorite avatars came from *loose* briefs where the engine authored unsupervised; the over-specified ones were the weakest.

**Batch interlocking fixes ruthlessly.** Lovable credits are finite. On free-only days the throughput floor is roughly one meaningful bundled prompt. Keep the highest-value bundle teed up first.

---

## Session close — THE ERGONOMIC LAW

Docs are regenerated **once, at session end** — never as running updates throughout.

**The governing principle: the labor lives with jAIne, not Scott.** jAIne can see the repo, so regenerating a whole doc is cheap for jAIne and zero-effort for Scott. Scott's job at close is exactly three moves: **copy, paste, commit.** Nothing else. If any doc update requires Scott to *read* the doc, *find* a spot, *judge* placement, or *strip* an instruction before pasting — the boat is leaking and the delivery is wrong.

**Delivery format — hard rule:**

- **Full-doc regenerations are delivered as FILES / ARTIFACTS, never as in-chat copy blocks.** A long markdown doc pasted into chat fights the renderer and arrives garbled (a fenced block spills its own fence; the diagram breaks into prose). Scott opens the artifact, selects all, overwrites the repo file, commits. This applies to status, parking-lot, master-spec, and any playbook.
- **Only `decisions.md` comes as an in-chat copy block** — because it's a small top-append, not a whole file. Even then, the block is *only the entries*, and the "paste at top" instruction lives in jAIne's message, never in the block.

**How each doc is delivered:**

1. **`status.md`** — **full rip-and-replace, as a file.** jAIne regenerates the whole doc. Scott overwrites and commits. Scott never reads it to update it.
2. **`parking-lot.md`** — **full rip-and-replace, as a file.** Same. Never "insert this bullet between X and Y" — that's a hunt, not a paste. Regenerate the whole doc.
3. **`master-spec.md`** — **full rip-and-replace as a file, but only when design truth changed.** "We shipped X" is status; "X works like this" is spec. **jAIne makes this call and states it out loud** ("spec: no change" or "spec: changed, here's the file") — Scott never has to judge whether the spec needs touching at the end of a long session.
4. **`decisions.md`** — **top-append only** (it's append-only history; it can't be regenerated). jAIne delivers a **clean in-chat copy block of just the new entries**, formatted exactly as they sit in the file. Scott pastes at the very top, below the format header. **The "paste at top" instruction lives in jAIne's chat message, NEVER inside the copy block** — nothing pasteable ever contains an instruction Scott has to delete.
5. **Commit.** The commit message is the session log.

**The rules that fall out of the principle:**
- **Full docs are files, not chat blocks.** Chat blocks are for small top-appends only (decisions.md).
- **Copy blocks never contain instructions.** Any "where / how" lives in jAIne's surrounding message text, never in the pasteable content. A block that needs editing before it pastes clean is a failed delivery.
- **Default to full-replace.** Full-replace is zero cognitive load for Scott (overwrite) and the cost is entirely jAIne's (regenerate). "It's more work" is irrelevant when the work is jAIne's. Only decisions.md is exempt, because history can't be overwritten.

**At close, jAIne states what changed** as the artifacts are delivered — a one-line reconciliation ("status replaced · one decision appended · spec unchanged · parking-lot replaced"). This is jAIne labeling jAIne's own output, not a checklist Scott has to run. It exists so a miss is visible in the moment; it costs Scott nothing.

---

## The standard

**"Shipped" means a completed end-to-end loop.** Not a screen rendering in a screenshot. Not assets banked on a phone. Design-complete ≠ shipped, and the docs must say so plainly, because the gap is where projects quietly rot.

### Sync before reading — the Code-job open

A Claude Code recon is only as good as the tree it read. **Before Code reads the codebase, it syncs to `origin/main` — a fresh `git pull` (or a clean clone). A local working clone is a disposable scratch checkout, never a source of truth.**

Why this is a hard rule, not a suggestion: a stale local clone fails *silently*. It doesn't error — it answers from an old tree and makes the recon confidently wrong. Learned 2026-07-17: a recon opened by concluding "wall/display mode is never built" because the clone was 78 commits behind origin, and only self-corrected because it thought to check origin. Same failure shape as `raw.githubusercontent.com` serving stale content over the GitHub API, same resolution: **the authoritative source wins, the cache never does.** For docs, the API beats the CDN. For code, `origin/main` beats the local clone. Code still keeps a working tree (it needs one to grep and trace imports); what's forbidden is *trusting* a tree that hasn't been synced this session. Step zero of every recon: pull. *(LOCKED, decisions.md 2026-07-17.)*

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

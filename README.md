# Emberhold

A gamified household operating system. Chores become **quests** worth **embers**; kids claim and complete them, adults approve, embers buy real rewards at the **Vault**. Mobile-first installable PWA, live at **theemberhold.com**.

**Stack:** Lovable.dev (React + Supabase) · Stripe Checkout · Copilot Create (assets) · Claude Code (codebase work)

---

## Read this first

**Agents:** fetch `canonical-manifest.md` before doing any work. It lists every doc, its status, and its raw URL, plus the fetch protocol. **Use curl with a cache-buster; never `web_fetch`; never fall back to memory.**

**Humans:** read `docs/north-star.md`. It's the index card — why the project exists, where we are on the ladder, and which doc to open next. Ninety seconds.

---

## The manifest

Every doc has exactly one job. If two docs answer the same question, one of them is wrong.

| Doc | Its one job | Changes |
|---|---|---|
| **`canonical-manifest.md`** | **The index.** Every doc, its status, its raw URL, the fetch protocol. | When a doc is added |
| **`docs/north-star.md`** | **Why**, and in what order. The gate ladder. | Rarely |
| **`docs/master-spec.md`** | **What it is.** Canonical design truth. | When a decision lands |
| **`docs/status.md`** | **Where the build is and what's left.** The single status board. | Every session |
| **`docs/parking-lot.md`** | **What might be.** Captured, not committed. | Every session |
| **`docs/decisions.md`** | **What we decided and why.** Append-only. | When a decision lands |
| **`docs/playbooks/`** | **How to do a thing.** Reusable method. | When method changes |
| **`docs/archive/`** | Reasoning history. Superseded but not deleted. | Never (append only) |

### Playbooks
| Playbook | Covers |
|---|---|
| `session-protocol.md` | How a build session runs. Read before working. |
| `avatar-generation.md` | How to generate on-band avatars. Hard-won; do not improvise. |
| `qa-harness.md` | How QA runs, regardless of which model shows up. |
| `phase-b-brief.md` | How to execute the stranger-proofing gate. |

---

## Rules of the repo

1. **Filenames are undated.** Git owns history. If you find yourself writing `-2026-07-10` in a filename, you're recreating the problem this repo exists to solve.
2. **Docs are replaced, not appended** — except `decisions.md` and `archive/`, which are append-only.
3. **One question, one doc.** Status lives in `status.md`. Not in three places.
4. **The spec is design truth, not status.** "We shipped X" goes in `status.md`. "X works like this" goes in `master-spec.md`.
5. **Session docs are written at session END**, not throughout.
6. **Commit message = what changed and why.** That's the session log; you don't need a separate one.

---

## The lanes

- **Scott** — vision, taste, final decisions.
- **jAIne** — hears, challenges, breaks down, translates intent into direction (build prompts, debugging, spec stewardship).
- **Lovable** — engineers the functional output.
- **Claude Code** — reads the codebase. RLS audits, security fixes, anything Lovable can't do from inside itself.

**The principle:** trust each lane; don't do each other's jobs. jAIne's failure mode is over-specifying when under-trusting downstream. Brief the **floor + intent**; leave **latitude on execution**.

---

## "Shipped" has a standard

A completed **end-to-end loop**. Not a screen rendering in a screenshot. Design-complete ≠ shipped.

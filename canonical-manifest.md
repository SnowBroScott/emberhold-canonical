# Emberhold — Canonical Manifest

*Single source of truth for all Emberhold documents.*

Repo: `https://github.com/SnowBroScott/emberhold-canonical`
Branch: `main`

---

## Fetch protocol

Fetch this manifest before doing any work. Then fetch the documents you need.

**Use `bash_tool` with `curl`. Not `web_fetch`** — web_fetch adds its own caching layer on top of GitHub's CDN, and stale content is silent: it doesn't error, it just quietly makes you wrong.

**Always append a cache-buster.** `raw.githubusercontent.com` caches on the URL for several minutes. A unique query string gives the CDN nothing to match against, so it has nothing stale to serve:

```bash
curl -sL "<RAW_URL>?cb=$(date +%s)"
```

**The rules, in priority order:**
1. If a fetch **fails**, stop and report it. Say so plainly.
2. **Never fall back to memory.** An agent that quietly answers from training when the fetch failed is worse than one that errors.
3. If content still looks stale despite the cache-buster (it shouldn't), stop and request a manual paste. **Manual paste is authoritative.**
4. Static uploads in the Claude Project are **not** authoritative. This repo is.
5. `LOCKED` takes authority over `DRAFT`. `DEPRECATED` has no authority.

---

## Documents

| Doc | Job | Status | Raw URL |
|---|---|---|---|
| **north-star** | **Why**, and in what order. The gate ladder. **Read first, cold.** | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/north-star.md` |
| **master-spec** | **What it is.** Canonical design truth. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/master-spec.md` |
| **status** | **Where the build is + what's left.** The single status board. | LIVE | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/status.md` |
| **decisions** | **What we decided and why.** Append-only. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/decisions.md` |
| **parking-lot** | **What might be.** Captured, not committed. | LIVE | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/parking-lot.md` |

### Playbooks — *how to do a thing*

| Playbook | Covers | Status | Raw URL |
|---|---|---|---|
| **session-protocol** | How a build session runs. Read before working. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/playbooks/session-protocol.md` |
| **avatar-generation** | How to generate on-band avatars. Hard-won; do not improvise. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/playbooks/avatar-generation.md` |
| **qa-harness** | How QA runs, regardless of which model shows up. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/playbooks/qa-harness.md` |
| **phase-b-brief** | How to execute the stranger-proofing gate. | LOCKED | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/playbooks/phase-b-brief.md` |

### Archive — *reasoning history, superseded but not deleted*

| Doc | Status | Raw URL |
|---|---|---|
| **hiatus-wrap-2026-07-09** | ARCHIVE | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/archive/hiatus-wrap-2026-07-09.md` |
| **operational-readiness-2026-07-02** | ARCHIVE — *superseded by north-star's gate ladder; kept for the full detail behind it* | `https://raw.githubusercontent.com/SnowBroScott/emberhold-canonical/main/docs/archive/operational-readiness-2026-07-02.md` |

---

## What to fetch, by session type

| Session | Fetch |
|---|---|
| **Any session, cold open** | manifest → north-star → status |
| **Build session (Lovable)** | + master-spec, session-protocol |
| **Security / Claude Code** | + status, phase-b-brief, qa-harness |
| **Avatar work** | + avatar-generation, master-spec |
| **Design / decision work** | + master-spec, decisions, parking-lot |

---

## Notes

- **Filenames are undated.** Git owns history. If you're about to write `-2026-07-10` into a filename, stop — that's hand-rolled version control, and it's the exact failure this repo exists to prevent.
- **This manifest carries no "last updated" date.** Git knows. A hand-maintained date field in a version-controlled file is the same disease, wearing a smaller hat.
- **Docs are replaced, not appended** — except `decisions.md` and `archive/`, which are append-only.

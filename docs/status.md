# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-12** (Claude Code proven as a build lane · feed vocabulary landed live · **the write-side policy layer found missing — 7 findings** · email confirmation turned on, signup broken, fixed forward · **Hold Alpha built — the audit is unblocked**)

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

**What's missing is not a module.** But as of today the shape of what's missing is much clearer than it was, and it is worse than we thought:

> **The write-side of the RLS policy layer was never systematically authored.** Reads were scoped to household. Writes got Supabase's defaults and good intentions. **Seven confirmed findings, one CRITICAL, one root cause.**

And a second, separate structural gap:

> **Onboarding ends at setup, not at activation.** A new household lands on a board that says "all quiet" three ways and never says what to do next.

See `north-star.md` for the gate ladder. Gate B just got teeth.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| ~~1~~ | ~~Test whether Claude Code can do BUILD work~~ | ✅ **DONE 2026-07-12. It can.** See below. |
| ~~2~~ | ~~Hold Alpha — the attacker household~~ | ✅ **DONE 2026-07-12.** `Ember Better Self` / Tucker + Dale. Armed with quests, a reward, an approved completion, minted embers, and `activity_log` rows. |
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1)** | Distribution. The mortal-peril item. **Now fully unblocked for the first time.** |
| **2** | **Fix the seven findings** | Distribution |
| **3** | **Avatar transport** — *generation ✅ · slice ✅ · upload + picker + free/paid split remain* | The Guildhall, the delight layer |
| **4** | **Pip first-run onboarding screens** | Gate D. Activation. Day-8 retention. |
| **5** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

---

## 🔴 THE SEVEN FINDINGS — one root cause

**Hypothesis, load-bearing:** the policy layer was built read-side-first. Every one of these is the write-side missing. **Assume there are more.** The audit's job is no longer to confirm these — it is to find the ones we haven't found.

| # | Finding | Severity | Source |
|---|---|---|---|
| 1 | **Anyone with a household join code can join as a full Parent admin.** No expiry, no rotation, no approval gate. Possession of the string = adult admin. | **CRITICAL** | Lovable deep scan |
| 2 | **Adult PIN lock is bypassable** — it isn't tied to real permission checks. A UI gate, not a policy. | Warning | Lovable deep scan |
| 3 | **`activity_log` — uncontrolled writes.** SELECT policy only; no INSERT/UPDATE/DELETE. Forged log entries possible. The log carries `ember_delta`. | Warning | Lovable scan |
| 4 | **`families` — no INSERT or DELETE policies.** | Warning | Lovable scan |
| 5 | **SECURITY DEFINER functions callable by signed-in users** — no internal-caller gating. | Warning | Linter |
| 6 | **Ember minting on approval is enforced by UI convention, not by policy.** | CRITICAL (unrated) | Ours (pre-existing) |
| 7 | **`rewards` INSERT scoped to OWNER, not to ADULT role.** Non-owner adults cannot stock the Vault. | HIGH | Ours (2026-07-12) |

**Do NOT press Lovable's "Try to fix all."** It will write plausible policies for the symptoms it can see, leave the ones it can't, produce no reviewable diff, and leave you believing you are fixed. Join-code policy in particular needs a *design decision* (expire? rotate? admit-on-approval?) that no linter can make.

*Exploit specifics for finding 6 remain OUT of this public repo — they live in the private note handed to the Claude Code session. Findings 1–5 and 7 are safe to publish: over-narrow grants and missing policies disclose nothing an attacker couldn't already probe.*

---

## 🔴 AUTH & EMAIL — new territory, opened today

- ✅ **Email confirmation is now REQUIRED.** Auto-confirm was on since inception. Every account in the product was created with zero proof of email ownership.
- ✅ **Signup broke instantly when it was turned on, and was fixed forward.** The app had no path for `{ user, session: null }`. Now: an on-voice holding view (*"ALMOST LIT — check your email"*), resend with a 45s cooldown, spam guidance, an escape hatch, duplicate-email handling, and unconfirmed-sign-in handling.
- ✅ **Password reset works.** Verified live. It was in the shipped column untested — it happened to be true.
- ⬜ **Auth email is unbranded and lands in spam.** Sender is `no-reply@auth.lovable.cloud`; display name is **`Family-Quest-Board`** — Lovable's scaffolding placeholder, a name that exists nowhere in the product. **This is the first artifact a stranger ever receives from Emberhold; it precedes the app itself.** The password-recovery mail landed in Junk. Templates are editable in Lovable Cloud → Emails. **Gate B/D blocker.**
- ⬜ **Cross-device confirmation was creating an empty household** — believed fixed by the same prompt (the phone was running pre-fix code). **Needs a clean single-device phone-only P1×L1 to confirm.**
- ⬜ **Minimum password length raised 6 → 8; re-auth for password change ON.** *(Verify these persisted.)*
- ⬜ **Orphaned auth users from testing** — `scottydawg@gmail.com`, `scott.draper83+alpha@gmail.com`. Delete.

---

## 🔵 THE BUILD MODEL CHANGED TODAY

**Claude Code can do frontend build work.** Proven end to end: Code wrote → pushed → Lovable pulled → renders live. **The Lovable credit ceiling is no longer the binding constraint on frontend throughput.**

- **Still unproven — do not assume:** whether Lovable *runs* a migration pushed from outside. `supabase/` was deliberately untouched in every job today. **Backend through Code remains an open question, and it is the whole ballgame.**
- **Two writers, one repo.** Lovable ran an audit *while* Code's work was in flight. Harmless today (read-only); it won't stay harmless. **One writer at a time.**
- **Batching is dead** for Code jobs. Small, single-concern, one commit each.
- **Usage is the new meter, not credits.** The Claude 5-hour window is the instrument. Don't put a heavy Code job and a working session in the same window — they contend.

---

## 🟡 PENDING VERIFY

- 🟡 **Lists collapsible sections** — landed (`fb6aa99`), rendered, looks right. Not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — the whole confirmation flow on ONE device, cold. The most common real-world path, still unproven on the fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. Still never confirmed.
- ~~🟡 Adult store catalog→curated flip~~ — ✅ **VERIFIED 2026-07-12.** Flip correct, adult instant-redeem works, no approval card generated.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **The seven findings above.** Audit first, then fix. **Claude Code job. Auto-accept OFF.**
- ⬜ **Auth email branding + deliverability.** See above.
- ⬜ **Service worker + app-shell cache** — no functional SW (airplane-mode confirmed). Makes "installable PWA" true instead of aspirational.
- ⬜ **Backup posture — REWRITTEN.** ⚠️ *The old item assumed a Supabase project Scott owned and could upgrade. **He doesn't.** The backend is **Lovable Cloud** — Lovable-managed Postgres, reachable only through their Cloud tab. There is no separate Supabase dashboard.* **Backups, plan tier, PITR, data export, and the ability to LEAVE are all Lovable's to grant.** The code has a Git backup. **The data has none, and there is no confirmed path to one.** Gate B blocker with a new shape.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport — UPLOAD + PICKER + SPLIT.** 48 individual 512×512 PNGs, named, in the repo. Remaining: Supabase storage upload → picker slotting → free/paid split. **Good night-shift Code job — bounded, reversible, eyeball-verifiable — EXCEPT the free/paid split, which is a product decision (the Guildhall's boundary) and is NOT an agent's to make.**
- ⬜ **Pip first-run onboarding screens** (see `decisions.md`). Three screens + the empty-board doorway fix.
- ⬜ **Empty board must be a doorway, not a eulogy.** "No quests yet — post your first with +." The guard against the skip path. ~4 words of copy.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Test-object cleanup** — `Testi`, `testy`, `Daily test`, `iopuyiouh`. *Data, not code — a manual DB delete, not a Code job.*
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility still open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Quick Add should default EXPANDED on an empty board.** Zero quests = the user has never seen one. Same state-driven pattern as the Vault's catalog/curated flip; just not applied here.
- ⬜ **`TITLE` → `QUEST TITLE`** on the create form. By the time you're scrolled to the field, the header is gone.
- ⬜ **`vault.tsx` TIER_LABEL dedup** — ✅ **DONE 2026-07-12** (`7132605`).
- ⬜ **Lists — "5 OPEN · 348 DONE."** 348 completed grocery items is a fossil layer. Cosmetic, but absurd to a stranger.
- ⬜ **Pip help discoverability** — the Pip icon isn't read as help.
- ⬜ **Pip install tutorial** — platform-detected Add-to-Home-Screen card.
- ⬜ **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **List suggestions in onboarding**.
- ~~⬜ Campaign detail view bug~~ — **NOT REPRODUCIBLE.** Code read `campaign.$id.tsx` closely: the quest fetch is already `.eq("campaign_id", id).eq("archived", false)`, it is the only source feeding the list, and git history shows no recent change. **The status doc was wrong.** If the symptom recurs, suspect the LIST view's count query, not the detail — i.e. the inverse of what was written.
- ~~⬜ Password confirm-entry on signup~~ — **ALREADY SHIPPED** (`dc74612`). The status doc didn't know. Full confirm field, mismatch validation, submit guard.

---

## 🅿️ PARKED

Unchanged — see `parking-lot.md`. Display/wall mode · #8b admin-reporting surface · kid-vs-kid impersonation · kid-auth (declined) · Ranks as a household dial · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor.

---

## ✅ SHIPPED — 2026-07-12

**Claude Code proven as a build lane**
- ✅ **The trial job landed live.** Feed vocabulary bundle: `ROUTINE`/`MILESTONE` → the real Dim/Warm/Hot/Blazing ramp, derived from `ember_delta` at render; `BOUNTY POSTED` → `QUEST POSTED`. Commit `c9069a6`. **Code wrote → GitHub carried → Lovable pulled → the browser rendered it.**
- ✅ **Code outperformed the brief.** It found `Briefing.tsx` already had a correct private `feedHeat()`, promoted it to a shared helper in `quest-helpers.ts` rather than duplicating the fix, and left a scoped, justified follow-up instead of silently absorbing it.
- ✅ **It refused to guess, twice, unattended.** On a 4-item unsupervised run it stopped on two tasks rather than invent changes — and checked git history to distinguish "never broken" from "already fixed." **It audited the status doc and was right both times.**
- ✅ **`vault.tsx` TIER_LABEL dedup** (`7132605`) · **Lists collapsible sections** (`fb6aa99`) — the latter built from a deliberately loose brief, unsupervised, correct on the first pass.

**Auth**
- ✅ Email confirmation required. Holding-state signup flow built and live. Password reset verified working. Min password length 6→8; re-auth on password change ON.

**Hold Alpha — the instrument**
- ✅ **A second, isolated household exists**, with its own owner, roster, quests, a reward, an approved completion, minted embers, and `activity_log` rows. **P4×L8 can finally be run as an attack, not a code review.**

**Cold-start findings (P1×L1 / L6), from walking it as a stranger**
- ✅ Onboarding ends at setup, not activation. Empty board is a dead end.
- ✅ Auth email is unbranded, off-voice, and spam-filtered.
- ✅ Quick Add should expand on an empty board; `TITLE` needs to say `QUEST TITLE`.
- ✅ Quality is a rating nothing consumes.

**The real user base was discovered**
- 🔵 **13 accounts exist. Not four.** Scott's guild and close confidants have been in the app for two weeks, signing in as recently as 3 days ago. **`north-star.md` says "every user to date shares a last name with the builder." That has been false for two weeks.** Gate E has a warmer start than the docs believe. *Fix the north-star.*

---

## ✅ SHIPPED — 2026-07-11

**Engine** — daily respawn verified; the quest lifecycle is now verified end-to-end.
**Vocabulary law** — `OPEN BOUNTY` chip killed via a real grep audit (Lovable's "already consistent" self-report was demonstrably false); `XP` eliminated from the install prompt.
**Render** — safe-area inset fix.
**Avatars** — the roster regenerated from scratch: 48 characters, 19 sheets, ~600px each; the old "roster boards" were found to be contact-sheet previews, not masters. Sliced by Claude Code into 48 individual 512×512 PNGs. The avatar-generation playbook was rewritten.
**Claude Code** stood up (native Windows) and passed its first job. **Lovable ↔ GitHub sync connected** — the first backup the app codebase has ever had.

---

## ✅ SHIPPED — 2026-07-10

**Engine bundle** — adult quest auto-approval · daily recurring board fix · edit-quest form parity.
**Copy sweep** — XP killed as user-facing vocabulary; pre-auth possessive "hold" copy reworked.
**Vault kid view** — dual-mode catalog/curated, both verified live.
**Vault adult view** — three zones (approval queue · your store · manage). Reporting removed.
**Couples reward rail** (`reward.audience`) — first schema change of the run; migration safety verified.
**Quest audience filter + badges + copy.**
**Security** — an over-broad anon EXECUTE grant on a data-mutating RPC revoked.
**Docs** — master spec brought current; `north-star.md` created; migrated to this repo.

---

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Assets later found to be previews; regenerated 7/11.*
- **2026-06-29 (eve)** — Lists v1, shopping-list import, invite share + deep link, notification bell, PIN/password recovery, collapsible avatar picker, the Avatar Style Spec.
- **2026-06-29 (AM)** — Activity feed spine, feed on the Briefing, monthly recurrence + audience flag, Parent/Family → Adult/Hold sweep.
- **2026-06-28** — Brand marks, Open Bounties strip, multi-day events, FAB-becomes-submit, avatar roster v1, Pip onboarding, Combat→Forge.
- **2026-06-27** — Campaigns, Calendar, the Briefing, member identity colors, login rewrite, juice pass 1.
- **2026-06-26** — The Vault, starter quest library, fail-closed PIN, Parent Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks, profile, quest detail, `+` FAB, brand.

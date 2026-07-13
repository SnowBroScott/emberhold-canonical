# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-07-12 (eve)** — *a learning session. Nothing shipped.* Avatar crop automation attempted through Claude Code and **abandoned after four escalating failures** · Code's lane boundary found · **avatar transport recon complete — it is frontend-only** · three latent problems surfaced that no one was looking for · the auth-email call reversed

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete.** Engine, economy, Vault (dual-mode both roles + adult-only rail), Campaigns, Calendar, Briefing/Hub seed, activity-feed spine, Pip onboarding, Lists, invite/join, notifications, PIN recovery — a full product loop is live at theemberhold.com.

**What's missing is not a module.** Two structural gaps, both still open:

> **Security — seven unpatched findings in the authorization policy layer.** One CRITICAL. Shared root cause. Specifics held privately; will be republished when fixes land.

> **Onboarding ends at setup, not at activation.** A new household lands on a board that says "all quiet" three ways and never says what to do next.

See `north-star.md` for the gate ladder.

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **P4×L8 — the tenant-isolation audit (Workstream 1)** | Distribution. The mortal-peril item. Fully unblocked; Hold Alpha exists. |
| **2** | **Fix the seven findings** | Distribution |
| **3** | **Avatar transport** — *generation ✅ · slice ❌ (redo by hand) · upload + picker + split remain* | The Guildhall, the delight layer |
| **4** | **Pip first-run onboarding screens** | Gate D. Activation. Day-8 retention. |
| **5** | **Auth email branding + deliverability** | **Promoted.** It is the first artifact a stranger receives, and it lands in spam. Thirteen people have already received it. |
| **6** | **Founding Guildhall build** (Stripe + webhook + entitlement) | Money |

---

## 🔴 GATE B — OPEN POLICY-LAYER FINDINGS

Seven confirmed findings, one CRITICAL, one shared root cause.

**Specifics are held privately until patched.** They live in the working note handed to the Claude Code audit session. Publishing an unpatched vulnerability map for a live application with real users is not transparency, it is an invitation. They will be republished once fixed.

Status: audit not yet run. Fixes not yet written.

---

## 🔴 AVATAR TRANSPORT — the recon is done, and it is not what we thought

**The good news: it is a frontend-only job.** Confirmed by direct read of the app repo (`C:\Users\snowb\projects\theemberhold`), not inferred:

- **Avatars are static files bundled by Vite.** `src/assets/avatars/avatar-01.png` … `avatar-21.png` (id `11` retired), imported as ES modules. **Zero Supabase Storage involvement** — grepped `storage.from(`, `getPublicUrl`, `bucket` across `src/` and `supabase/`: no matches.
- **The picker reads a hardcoded array.** `HERO_AVATARS` in `src/components/emberhold/Avatar.tsx` — one hand-written entry per import. `AvatarPicker.tsx` just filters it by group.
- **The choice persists as a string convention.** `profiles.avatar_emoji text NOT NULL DEFAULT '🙂'` — overloaded to hold either a literal emoji **or** the string `"hero:" + id`. No CHECK constraint, no FK. Swapping which ids exist requires **no schema change.**
- **There is no gating of any kind.** Grepped `premium|locked|unlock|tier|entitlement|founding|guildhall|gated` — zero hits in any avatar file. Confirmed absent, not assumed.

### But the recon surfaced three problems nobody was looking for

**🔴 `Feast` is still in the code.** The **Feast → Hall rename was decided 2026-07-03** and never landed. Ten days later `AvatarPicker` still filters on `Forge / Garden / Keep / Feast`. **A LOCKED decision failed to reach the product and nothing caught it.** *What else from that day didn't land?*

**🔴 This is a 20 → 48 EXPANSION, not a swap.** The app has 20 avatars across four groups of 4–6. The new roster is 48, twelve per class. Every plan that used the word "replace" was quietly wrong about the shape.

**🔴 Thirteen accounts already hold `hero:` picks.** If the new roster reuses ids `01`–`12` with entirely different art, **every existing member's avatar silently becomes a different character.** Scott's kids included. Precedent exists for the fix: migration `20260628220717` force-reset `hero:11` to the default emoji when that avatar was retired. **The same move is required here — and it is not damage control, it is a re-forge moment.** Thirteen people get a reason to open the app: *"The hold has been re-forged. Choose your hearthmate."*

**Consequence:** the avatar transport **does** touch `supabase/` — not schema, just a one-line `UPDATE` to invalidate stale picks. Which makes it a low-stakes, precedented **first test of whether Lovable executes an externally pushed migration** — the open question the build model hinges on.

### The slice is being redone BY HAND

**Four automated attempts, four failures.** Not recorded as an embarrassment — recorded because the failure mode is instructive and will recur.

| Attempt | Method | Failure |
|---|---|---|
| 1 | Fixed 236px radius, centered | 0.30 confidence on all 48. Fell back to a heuristic on every file, spot-checked two, declared success. |
| 2 | Ray-cast to the rim | r=74–126 on a 512 frame. Locked onto interior bright features — lava cracks, glowing eyes — not the rim. |
| 3 | "Outermost non-background pixel" | r=219–247. **The contamination IS non-background.** It grew a circle *around* the fringe and reported 0.97 confidence. |
| 4 | Re-slice from the source sheets | Found **64 circles across 19 sheets** (should be 57). Five on one sheet. Circles running off the sheet edge. |

**The root cause is not the algorithm.** The success criterion was *"does this look right,"* and **neither Claude Code nor jAIne can see.** Two blind agents converging confidently is not a review process.

- 🟡 **Scott cuts the roster by hand in GIMP.** Ellipse-select fixed 1:1, snapped to the gold rim and **biased 2–3px INWARD** — losing a sliver of rim is free; keeping a pixel of a neighbour is not.
- ✅ **Nothing was overwritten.** The 19 source sheets and the original 48 slices are intact. **Time was lost; assets were not.** The sheets being banked is the reason.

---

## 🔵 THE BUILD MODEL — refined

**Claude Code can do frontend build work.** Proven 2026-07-12 (AM): Code wrote → GitHub carried → Lovable pulled → the browser rendered it. The Lovable credit ceiling is no longer the binding constraint on frontend throughput.

**But the lane has an edge, and we found it tonight.**

> **Code's lane is TEXT.** Code, config, strings, files, structure — anything whose correctness it can verify by *reading*. **The moment the success criterion is "does this look right," it is outside the lane**, and no brief fixes that, because the briefer can't see either.

- **Model routing by job type** — the meter is usage, not credits, and a mechanical grep does not need a frontier model:

  | Job | Model |
  |---|---|
  | Recon, greps, file traces, mechanical edits, file drops | **Haiku** |
  | Build jobs with real latitude (loose brief, authored output) | **Sonnet** |
  | **The tenant-isolation audit** | **Opus.** Do not optimize here. |

- **One writer at a time.** Lovable ran an audit *while* Code's work was in flight. Harmless because it was read-only. It won't stay harmless.
- **Batching is dead** for Code jobs. Small, single-concern, one commit each.
- **Still unproven — do not assume:** whether Lovable *runs* a migration pushed from outside. **The avatar `hero:` reset is the cheap first test.**

---

## 🟡 PENDING VERIFY

- 🟡 **Lists collapsible sections** — landed (`fb6aa99`), rendered, looks right. Not yet exercised across a full session.
- 🟡 **Cross-device / phone-only signup** — the whole confirmation flow on ONE device, cold. The most common real-world path, still unproven on the fixed code.
- 🟡 **Phaeaz cold-account retest** — open since the hiatus. Still never confirmed.
- 🟡 **Min password length 6→8; re-auth on password change ON** — verify these persisted.

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **The seven findings above.** Audit first, then fix. **Claude Code job, Opus, auto-accept OFF.**
- ⬜ **Auth email branding + deliverability.** Sender is `no-reply@auth.lovable.cloud`; display name is **`Family-Quest-Board`** — Lovable scaffolding, a name that exists nowhere in the product. **It lands in spam.** Templates are editable in Lovable Cloud → Emails. **Custom sender requires DNS records at Porkbun (free — DNS is not a paid product) plus an SMTP provider (Resend free tier: 3,000/mo, 100/day, one domain — orders of magnitude above need).** ⚠️ *Any NS-record request must be inspected before pasting — nameserver delegation is not the same as adding a TXT record.*
- ⬜ **Service worker + app-shell cache** — no functional SW (airplane-mode confirmed). Makes "installable PWA" true instead of aspirational.
- ⬜ **Backup posture.** The backend is **Lovable Cloud** — Lovable-managed Postgres, reachable only through their Cloud tab. **Backups, plan tier, PITR, data export, and the ability to LEAVE are all Lovable's to grant.** The code has a Git backup. **The data has none.** Gate B blocker.
- ⬜ **Orphaned auth users from testing** — `scottydawg@gmail.com`, `scott.draper83+alpha@gmail.com`. Delete.

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Avatar transport.** Now fully scoped:
  1. **Hand-cut the roster in GIMP** (Scott). Bias inward.
  2. **File drop** → `src/assets/avatars/` — mechanical, **Haiku**.
  3. **Rewrite `HERO_AVATARS`** in `Avatar.tsx` — 48 entries + imports.
  4. **`Feast` → `Hall`** — label, enum value, animation-theme name. The 7/03 decision, ten days late.
  5. **Retire or rebuild `src/routes/avatar-review.tsx`** — it hand-duplicates the roster with its own array.
  6. **Invalidate stale `hero:` picks** — the migration. The re-forge moment. **NOT a silent swap.**
  7. **Free/paid split** — **a product decision. Never handed to an agent.** ⚠️ *The locked "16 free / 24 paid" arithmetic predates the 48-character roster and no longer adds up. Re-lock it.*
- ⬜ **Pip first-run onboarding screens** (see `decisions.md`). Three screens + the empty-board doorway fix.
- ⬜ **Empty board must be a doorway, not a eulogy.** "No quests yet — post your first with +." ~4 words of copy.
- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Test-object cleanup** — `Testi`, `testy`, `Daily test`, `iopuyiouh`. *Data, not code.*
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier). Consumer + kid-visibility still open. Binds to the weekly recap.

## ⬜ OUTSTANDING — polish

- ⬜ **Quick Add should default EXPANDED on an empty board.** Zero quests = the user has never seen one.
- ⬜ **`TITLE` → `QUEST TITLE`** on the create form.
- ⬜ **Lists — "5 OPEN · 348 DONE."** A fossil layer. Cosmetic, but absurd to a stranger.
- ⬜ **Pip help discoverability** · **Pip install tutorial** (platform-detected Add-to-Home-Screen card).
- ⬜ **Reward scarcity limits** · **Cheap Dim-tier starter reward** · **Yearly/monthly event recurrence** · **List suggestions in onboarding**.

---

## 🅿️ PARKED

See `parking-lot.md`. Display/wall mode · #8b admin-reporting surface · kid-vs-kid impersonation · kid-auth (declined) · Ranks as a household dial · photo avatars · cosmetic drop #2 (the roster surplus) · Capacitor.

---

## ✅ SHIPPED — 2026-07-12 (day)

**Claude Code proven as a build lane**
- ✅ **The trial job landed live.** Feed vocabulary bundle: `ROUTINE`/`MILESTONE` → the real Dim/Warm/Hot/Blazing ramp, derived from `ember_delta` at render; `BOUNTY POSTED` → `QUEST POSTED`. Commit `c9069a6`.
- ✅ **Code outperformed the brief.** Found `Briefing.tsx` already had a correct private `feedHeat()`, promoted it to a shared helper rather than duplicating the fix, and left a scoped follow-up instead of silently absorbing it.
- ✅ **It refused to guess, twice, unattended** — and audited the status doc, correctly, both times.
- ✅ **`vault.tsx` TIER_LABEL dedup** (`7132605`) · **Lists collapsible sections** (`fb6aa99`).

**Auth**
- ✅ Email confirmation required. Holding-state signup flow built and live. Password reset verified. Min password length 6→8; re-auth on password change ON.

**Hold Alpha — the instrument**
- ✅ **A second, isolated household exists** — `Ember Better Self` / Tucker + Dale — with its own owner, roster, quests, a reward, an approved completion, minted embers, and `activity_log` rows. **P4×L8 can be run as an attack, not a code review.**

**The real user base was discovered**
- 🔵 **13 accounts exist. Not four.** Scott's guild and close confidants have been in the app for two weeks, signing in as recently as 3 days ago. **Gate E has a warmer start than the docs believed.**

---

## ✅ SHIPPED — 2026-07-11

**Engine** — daily respawn verified; the quest lifecycle is now verified end-to-end.
**Vocabulary law** — `OPEN BOUNTY` chip killed via a real grep audit; `XP` eliminated from the install prompt.
**Render** — safe-area inset fix.
**Avatars** — the roster regenerated from scratch: 48 characters, 19 sheets, ~600px each. The avatar-generation playbook was rewritten.
**Claude Code** stood up (native Windows). **Lovable ↔ GitHub sync connected** — the first backup the app codebase has ever had.

---

## ✅ SHIPPED — 2026-07-10

**Engine bundle** — adult quest auto-approval · daily recurring board fix · edit-quest form parity.
**Copy sweep** — XP killed as user-facing vocabulary.
**Vault kid view** — dual-mode catalog/curated, both verified live.
**Vault adult view** — three zones (approval queue · your store · manage).
**Couples reward rail** (`reward.audience`) — first schema change of the run; migration safety verified.
**Quest audience filter + badges + copy.**
**Security** — an over-broad anon EXECUTE grant on a data-mutating RPC revoked.
**Docs** — master spec brought current; `north-star.md` created; migrated to this repo.

---

## ✅ SHIPPED — earlier (foundation)

- **2026-07-03** — Avatar Overhaul designed (Feast→Hall). *Assets later found to be previews; regenerated 7/11.* **⚠️ The rename itself never landed in code — found 7/12.**
- **2026-06-29 (eve)** — Lists v1, shopping-list import, invite share + deep link, notification bell, PIN/password recovery, collapsible avatar picker, the Avatar Style Spec.
- **2026-06-29 (AM)** — Activity feed spine, feed on the Briefing, monthly recurrence + audience flag, Parent/Family → Adult/Hold sweep.
- **2026-06-28** — Brand marks, Open Bounties strip, multi-day events, FAB-becomes-submit, avatar roster v1, Pip onboarding, Combat→Forge.
- **2026-06-27** — Campaigns, Calendar, the Briefing, member identity colors, login rewrite, juice pass 1.
- **2026-06-26** — The Vault, starter quest library, fail-closed PIN, Parent Quest Log.
- **Foundation** — the Engine, household/roles/classes, Ranks, profile, quest detail, `+` FAB, brand.

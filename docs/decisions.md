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

---

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
WHY: The core "embers mint only on adult approval" rule is currently enforced by UI convention rather than by
     policy. Confirmed finding, not introduced by the 7/10 migration. A naive role-check fix does not close it
     because of how kid profiles share the owner's session; the real fix keys on the acting profile + a
     verified-parent action + column-level gating, and requires reading the parent-verification path end to end.
     [Exploit specifics deliberately held OUT of this public repo — they live in the private security note handed
     into the Claude Code / Workstream 1 session.]
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

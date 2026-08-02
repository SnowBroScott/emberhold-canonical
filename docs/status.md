# Status
**Where the build is and what's left.** The single status board.

Last session: **2026-08-01 (late)** — *the free session.* **Zero Lovable credits. Five Code jobs, one publish, four glass verifications, one canon correction.** **The app is installable on Android for the first time: a minimal no-cache service worker is registered and running on the live origin.**

**The headline: `/sw.js` ships, the master-spec fold is done from a read, page titles use a middot, and three carried tracking items died on contact with the glass rather than being fixed.** Two of the three had never described anything real.

**THE SERVICE WORKER IS REGISTERED AND ACTIVATED.** `public/sw.js`, eighteen lines, four listeners: `install` calls `skipWaiting()`, `activate` calls `clients.claim()`, `fetch` is an empty handler, plus a comment block stating that caching here is a scope change requiring a decision. **It caches nothing and contains no reference to the Cache API.** Registration lives in `__root.tsx` alongside the existing auth effect, guarded on `"serviceWorker" in navigator`, registering immediately when `document.readyState === "complete"` and on the `load` event otherwise. **DevTools confirms activated and running, scope `/`, source `sw.js`.**

⚠️ **THE ANDROID INSTALL PROMPT HAS NOT APPEARED YET.** The worker is the criterion we were missing, not necessarily the only one. **The tablet was mid-restart when the session ended and the check was never completed.** *This is a pending verify, not a shipped install.*

**THE DIAGNOSIS COST FOUR TURNS AND THE ANSWER WAS "PUBLISH IT AGAIN."** `/sw.js` returned 404 with `text/html` while `manifest.webmanifest` and `robots.txt` from the same directory served fine. jAIne proposed a Nitro static-output theory, then a host-reserves-the-sw-path theory, both plausible and both wrong. **Scott republished on instinct and it resolved.** *Reach for the simple explanation before the defect is already written in this doc and was walked past twice in one exchange.*

**THREE CARRIED ITEMS DIED WITHOUT A FIX.** The orphaned calendar event: bounties do not create calendar events, so nothing can be orphaned. The Slate panel/header mismatch: there is no "Done today" header on the Slate, so the two cannot disagree — jAIne invented the label from a screenshot on 07-31. The standing-duties blurb: it is an **empty state**, not a persistent blurb, which changes the answer entirely. **A tracking item with no named evidence and no date is a rumor.**

**AND ONE REAL FINDING CAME OUT OF LOOKING AT THE SLATE FOR A DIFFERENT REASON.** The collapsed group labeled `5 more current` contains only rows that are **done** for the current occurrence. "Current" read as the opposite of what those rows are. **Now `{count} done`.** The finding surfaced while chasing a fake one.

**CANON CARRIED AN OVERSTATEMENT FOR ONE SESSION AND CODE CAUGHT IT.** The 08-01 recon reported the three verb switches as word-for-word identical; jAIne folded that into `master-spec.md` this session. **They are not.** `wall.tsx`'s `event_created` omits "to the calendar." **The recon answered the question it was briefed with — the five verbs — and jAIne generalized to the whole switch.** Corrected in the spec file before commit.

Last session (prior): **2026-07-31 (night) → 2026-08-01 (early)** — the marker session. ~2.8 credits.
Last session (prior): **2026-07-31 (day)** — the redemption-path session. Four free Code jobs.
Last session (prior): **2026-07-30 (night)** — the Slate + Ledger build. Five credits.

Key: ✅ DONE (verified) · 🟡 PENDING VERIFY · ⬜ OUTSTANDING · 🅿️ PARKED · 🔵 VALIDATED (no build needed)

---

## Where the platform is

**Structurally complete, published, installable on iOS, with a working activation path and a working spend path for every role.** Engine, economy, Vault, Campaigns, Calendar, Briefing/Hub, activity-feed spine, Lists, invite/join, notifications, PIN recovery, admit-on-approval, wall/display mode, the 48-avatar roster, a household-local date model, tenant isolation verified under live attack, clean function and table grant surfaces, the Slate, the Ledger, a rollover engine verified on a real month boundary, and **as of tonight a registered service worker.**

**Emberhold is a ONE-module product with ONE module.** Registers are aesthetic only. **Fitness (Forge) is the sole module, it is not built, and it is scoped to the Draper household.**

> ✅ **THE MASTER-SPEC FOLD IS DONE.** Four regions, from a read, not a grep: the `verbLabel` open question (answered), the marker read/write warning (shipped), `/quest-log` + `/hearth-log` (reclassified), the copy-discipline section (em-dash rule added). **773 lines to 782.** The regeneration flag stays closed.

> ⚠️ **RESIDUAL, UNCHANGED:** roughly 400 lines of master-spec — Part II (Forge), the onboarding sections, the schema detail — remain unread since the 07-29/07-30 folds. **A full cold read is worth doing eventually; it is not urgent.**

---

## 🔴 THE CRITICAL PATH

| # | Item | Blocks |
|---|---|---|
| **1** | **🟡 THE ANDROID INSTALL PROMPT.** The service worker is live; the prompt has not been seen. **Restart the tablet, load fresh, check the Chrome menu for "Install app."** If still absent, remote-debug over USB — Chrome's Manifest panel names the failing criterion in plain text. | Gate B. The wall device. |
| **2** | **🟡 Signup glass checks #2 and #3.** Cold join-path signup; original-tab path. | Gate B honesty. |
| **3** | **Founding Guildhall build** (Stripe + webhook + entitlement write). | Money. Gate C. |
| **4** | **Walk the kid joiner flow with an actual kid.** Unblocked 08-01. Built, published, three beats, never exercised end to end. **Scott's hands, scripted in advance.** | Gate B. |
| **5** | **Avatar paywall flip** (two data changes) — whenever Scott's ready. | — |

**Downgraded off the critical path: auth email.** Sender identity fixed by the project rename; one of six providers still spams. **Tracked, not worked.**

---

## ✅ SHIPPED — 2026-08-01 (late)

*Five Code jobs, zero Lovable credits.*

### The service worker *(`e813dca` + readyState follow-up)*

- ✅ **`public/sw.js`** — served at the origin root, default scope `/`, static file untouched by the bundler. Verified as a real 200 with a JS content type after republish.
- ✅ **`install` → `skipWaiting()`; `activate` → `clients.claim()`.** Deliberate: default update behavior parks a new worker in "waiting" until every tab closes, which on a wall tablet that never closes is forever. **These two lines are the kill switch, and a no-cache worker without one is a worse trade than no worker.**
- ✅ **The `fetch` handler is empty.** No `event.respondWith`, not even a pass-through — `respondWith(fetch(request))` is not a pass-through and it breaks range requests and re-wraps redirects.
- ✅ **Zero references to the Cache API.** No `caches.open`, no precache, no cache names.
- ⚠️ **The initial registration was a race.** The effect attached a `load` listener during hydration; if `load` had already fired, the listener never ran. **Passes on a slow load, fails on a warm one. jAIne's brief specified the load guard and owns the defect.** Fixed with a `document.readyState === "complete"` branch and a single shared `registerSW` function so there is one registration expression.
- ✅ **No PWA plugin, no Workbox, no vite-plugin-pwa.** Two hand-written files by design — a plugin generates a precache manifest, which is the thing being deliberately not built.
- ✅ **Repo had zero prior SW footprint.** Confirmed by read: no artifact, no `navigator.serviceWorker` reference, nothing in `vite.config.ts` or `package.json`.

### Page titles *(`fc3fad3`)*

- ✅ **`: ` → ` · ` across 38 title strings in 34 files.** `__root.tsx`'s `title`, `og:title` and `twitter:title` all three. Zero remaining `: Emberhold` matches.
- ⚠️ **`ledger.tsx` and `slate.tsx` each carry their own `og:title`; the other 31 routes do not.** Not a defect. **But it means two routes share to social differently from the rest** — either 31 are missing it or 2 have extra. **Parked to parking-lot, not chased.**

### The Slate label *(`50ba06e`)*

- ✅ **`{calm.length} more current` → `{calm.length} done`.** The collapsed group holds only rows completed for the current occurrence; **"current" said the opposite of what they are.** Count stays live. No pluralization branch — "1 done" and "5 done" both read.
- ✅ **No other surface holds this string.** Checked by read, because the copy-divergence pattern is now a known class.

### `member_admitted` and `member_denied` *(`b2efd8f`)*

- ✅ **Both cases added to all three switches** — `wall.tsx` `tickerLine`, `Briefing.tsx` `pulseLine`, `NotificationBell.tsx` `lineFor`. `${who} admitted ${obj}` and `${who} denied ${obj}`, matching the existing past-tense shape.
- ✅ **`hearth-log.tsx` deliberately excluded.** Debug surface, live consumer, Gate C.
- ⚠️ **`member_denied` has never rendered in production.** Nobody has been denied. **Its first real render will be its first test.**
- ⚠️ **THE THREE SWITCHES WERE NEVER BYTE-IDENTICAL.** `wall.tsx`'s `event_created` reads `${actor} added ${obj}`; the other two read `${actor} added ${obj} to the calendar`. **Still duplication rather than a correctness defect. But three copies that have already drifted once is a different claim from three that have not, and the shorter string is on the wall.**

### Items closed by looking, not by building

- ✅ **DELETING A BOUNTY CANNOT ORPHAN A CALENDAR EVENT.** Bounties do not create calendar events. **The item had sat open long enough that nobody remembered writing it, and "one look" made it cheap to defer rather than cheap to kill.**
- ✅ **THE SLATE PANEL/HEADER MISMATCH DOES NOT EXIST.** There is no "Done today" header on the Slate. A completed row reads `Current · next Aug 3`, dimmed, collapsed. **The panel reading UNCLAIMED is correct — it is unclaimed against its next occurrence.** jAIne invented the label from a 07-31 screenshot.
- ✅ **THE STANDING-DUTIES BLURB IS AN EMPTY STATE, NOT A BLURB.** `SlateEmpty()`, with a mascot and a CTA. **Code stopped on the brief's stop-clause rather than deleting it, which is exactly what the clause was for.** Deletion would have left a stranger with a blank section.

---

## ⬜ OPEN — the next work, in order

- ⬜ **🟠 THE `SlateEmpty()` REWRITE — BRIEFED, NOT YET RUN.** Current string: *"Nothing repeats yet. When a bounty comes back, like the trash, the dishes, or Monday laundry,it lives here as one line, forever. Post one and it'll show up."* **Three sentences doing three jobs; one earns its place.** "Nothing repeats yet" restates the empty condition the absent rows already show. "As one line, forever" is roll-forward mechanics explained to a nine-year-old. "Post one and it'll show up" duplicates the CTA directly below it. **Replacement: "Repeating bounties live here. The trash, the dishes, Monday laundry."** Mascot and CTA untouched. ⚠️ **Note the missing space in `laundry,it` — introduced by the em-dash sweep and shipped.**
- ⬜ **🖊️ THE SCREEN COPY PASS — RUNNING.** **Slate ✅ · Ledger ✅ · Auth ✅ · Campaigns ✅ · Calendar ✅ · Briefing ✅ · everything else unreviewed.**
- ⬜ **`progression.test.ts` carries a pre-existing `tsc --noEmit` error** — missing `vitest` types. **Has now polluted the output of five consecutive Code jobs.** Pure noise removal, but it means the next real typecheck failure is visible instead of buried.
- ⬜ **`logActivity` IS A CLIENT-SIDE BOLT-ON.** Every call site can forget, and one did. **The mechanism fix is the definer RPC writing its own log row.** Migration, so it waits for credits.
- ⬜ **`parent_self_redeem` INSERTS `status='approved'` OUTRIGHT.** An adult redeeming their own embers skips the approval gate. **By design per the code, never written down.**
- ⬜ **The recurrence chip reads `Monthly · 1st` on the Slate.** Confirm the board and create/edit agree.
- ⬜ **STALE chip predicate.** Likely `due_date < today`. **Probably closed by roll-forward — verify before building.**
- ⬜ **The Briefing makes the same claim twice** — an OPEN BOUNTIES strip and a Slate card. Cosmetic.
- ⬜ **The Briefing's FAB overlaps the Campaigns progress bar.** **Layout, Scott's eye.**
- ⬜ **`Testing redemption tracking` and `Testing retired` are both user-visible.** Prod test-object cleanup. ⚠️ **`Testing retired` sits in the Retired section and canon records no un-retire affordance.**
- ⬜ **DELETE THE THROWAWAY TEST BOUNTY.** "Do Not Approve Testing Roll-Over." Never approved, so Delete is available. **One tap. Still not done.**

---

## 🟡 PENDING VERIFY

- 🟡 **🔴 THE ANDROID INSTALL PROMPT.** Worker is registered and running. **Prompt not yet seen.** Tablet restart was in progress at session end. ⚠️ **Fully Kiosk Browser uses its own webview and will never show an install prompt — the check must run in Chrome proper.**
- 🟡 **🔴 THE MONTHLY ROLL BRANCH.** Aug 1 was an incidental exercise, not the verification. **The clean natural test is 2026-09-01.**
- 🟡 **The kid joiner flow has never been walked end to end by a kid.** Unblocked. **First-run fired and completed for Mia on a switch; the JOIN path is still unexercised.**
- 🟡 **`Testing retired` stays retired** once its successor's date arrives. Free, one look.
- 🟡 **The wall's `logActivity` sits in `mutationFn`, not `onSuccess`.** A failed log would report a failed approval that actually committed. **Compare against `vault.tsx`. One line.**
- 🟡 **`/create?recurring=true`** — the Slate's empty-state CTA is verified; the direct-URL half was not.
- 🟡 **The timezone heal — DRAFT until proven from a non-Pacific device.**
- ⚠️ **Wall adult-verified turn-in.** The approve half is exercised; **the wrong-PIN half is not.**
- 🟡 **Grant-revoke verification probe job — DRAFTED, DEFERRED NINE TIMES.** `SQLSTATE 42501` = FAIL; any application-level error = PASS.
- 🅿️ **`/setup/intent` — PARKED WITH A TRIGGER.** Finalized when Forge is built.
- 🟡 **The ember progress trail** · **Avatar render fallback — check the wall** · **Founder tier-tag verification (32 lock / 16 open)** · **Phaeaz cold-account retest** · **Min password length 6→8** · **Wall full end-to-end membrane loop.**

---

## ⬜ OPEN — Forge, from 2026-07-28

- ⬜ **🔴 THE CATALOG IS THE FIRST BUILD, AND IT IS A MAKE JOB.** Movement pattern · muscle attribution · equipment requirement · substitution map. **Generated offline, reviewed by Scott, shipped as data.**
- ⬜ **🔴 `progression.ts` has no progression axis except load.** **Pure TypeScript, ten tests, zero Supabase imports, zero credits.**
- ⬜ **The engine cannot accept a pre-session constraint.** Scope with the rep-compensation job.
- ⬜ **Equipment records need `exclusive` vs `shareable`.**
- ⬜ **Rep-compensation needs a validity floor.**
- ⬜ **The Smith machine offset is assumed, not measured.** **A wrong offset corrupts every Smith e1RM forever.**
- ⬜ **"Adults only" is a proxy for "trains" and will not hold.**
- ⬜ **`enabled_modules` has no consumer.**

## ⬜ OPEN — carried

- ⬜ **🟠 `quests.approved_by` is validated more weakly than `redemptions.decided_by`.** Neither trigger checks the written value names an adult. **Tolerable under the walk-up boundary, not correct.**
- ⬜ **Two derivations of role.** ⚠️ **And the third split stands: `useActiveMember().role` (client, switched profile) vs `has_role(auth.uid())` (server, always the owner). They disagree BY CONSTRUCTION whenever a kid is active.**
- ⬜ **What does `actor_label` mean?** **Design call, needs Scott.**
- ⬜ **`campaign.$id.tsx` gates quest creation on `isParent`; the FAB and QuickAddTray do not.**
- ⬜ **⚠️ `routeTree.gen.ts` drift is CONFIRMED LIVE.** Every build surfaces it; every agent has to know to throw it away. **Held across five Code jobs tonight without incident, because every brief named it.**
- ⬜ **The early-approval seam.** Approving a weekly before its due date produces a same-week successor.
- ⬜ **Bounty creation is ungated and DELIBERATELY STAYS THAT WAY.**
- ⬜ **`sandbox_exec`** — pre-existing platform role holding EXECUTE on every function in `public`. **Ask Lovable. One question.**
- ⬜ **`quests.due_date` still carries `DEFAULT CURRENT_DATE` — deliberately.** ⚠️ **It is server-side only. Users do not set due dates and cannot see them. Recurrence anchors are a different thing and ARE visible as chips.**
- ⬜ **Ghost successor cleanup.**
- ⬜ **`wall_request_redemption` is called from the Vault and its name lies.** Deliberate debt. **Rename when something else takes that function to Lovable.**

---

## 🟢 SECURITY TRIAGE

*Verdict-level only. Mechanism lives in the Code session, never here.*

**Ignored — validated, all correct:**
- ✅ **"Forgot PIN" takeover (CRITICAL) — FALSE POSITIVE.**
- ✅ **Join-code → Parent admin (CRITICAL) — FALSE POSITIVE.**
- 🔵 **Adult PIN lock not tied to real permission checks — KNOWN-ACCEPTED, CONDITIONALLY.**
- 🔵 **Redemption submitted on behalf of another member — BY DESIGN, CONDITIONALLY.** ⚠️ **`wall_request_redemption` checks only that `_profile_id` belongs to the household and is active; it never compares against `auth.uid()`. The Vault inherits that posture, and so does `mark_first_run_complete`.**
- ✅ **Signed-in users can execute SECURITY DEFINER (lint 0029) — PERMANENTLY IGNORED.**

**Fixed:**
- ✅ **`mark_first_run_complete` profile-scoping — CLOSED 2026-08-01.**
- ✅ **`approve_redemption` / `deny_redemption` attribution — CLOSED 2026-07-30.**
- ✅ Public/anon SECURITY DEFINER execute (lint 0028) · `founder_gate_enabled()` + `my_household_is_founder()` · `anon` CRUD on `families` · `anon` CRUD on the other fourteen tables · Adult PIN plaintext in `localStorage`.

**Real, open:**
- ⬜ **🔴 THE SERVICE WORKER IS NOW A SECURITY SURFACE THAT DID NOT EXIST YESTERDAY.** It caches nothing today, which is what makes it safe. ⚠️ **Any future caching work must NEVER cache a response carrying an Authorization header — that is tenant isolation reintroduced at the cache layer, on a boundary that was verified under live attack.** **And a caching bug is the one defect on this board that cannot be fixed by pushing a fix.**
- ⬜ **`quests.approved_by` accepts any in-family profile id, including a kid's.**
- ⬜ **`actor_label` display forgery — DOWNGRADED, RE-SCOPED AS DESIGN.**
- ⬜ **Kids read `adults_only` reward names/costs** and ⬜ **kids read `parents_only` quest details** — **same class; fix together**, *with* the own-session-vs-per-member-auth decision. **Top open security items.**
- ⬜ **`supabase_admin` default-privilege residual** — platform-scoped.
- ⬜ **Forge display mode is a semi-public surface.**
- ⬜ **`flock.js` analytics tracker in the app `<head>`.** **Must be named in the Gate C privacy policy.**

**Dependency scan:** `npm audit` = 0 against `package-lock.json`; the real lockfile is `bun.lock`. Run `bun audit`. ⚠️ *`package-lock.json` and `query_quest.mjs` are still sitting untracked in the working tree — confirmed again 08-01 late.*

---

## ⬜ OUTSTANDING — security & distribution

- ⬜ **Auth email deliverability.** Tracked-not-worked. ⚠️ *Inspect any NS-record request before pasting.*
- ⬜ **Rewards + quests audience reads** — fix *with* the own-session-vs-per-member-auth decision.
- ⬜ **Grant-revoke verification probe job.**
- ⬜ **Ask Lovable what `sandbox_exec` is.**
- ✅ **Service worker — SHIPPED for installability only. The offline shell is DEPRIORITIZED, deliberately.** See parking-lot.
- ⬜ **Backup posture.** Data has no backup; Lovable's to grant. A distribution blocker.
- ⬜ **Prod test-object cleanup.**

## ⬜ OUTSTANDING — ship-blocking debt

- ⬜ **Vault favorites → real per-profile persistence** — currently `localStorage`.
- ⬜ **Quality — a rating with no consumer.** Direction LOCKED (signal, never an ember modifier).
- ⬜ **Re-forge reach across the 13.**

## ⬜ OUTSTANDING — polish

⬜ **The `SlateEmpty()` rewrite (briefed)** · **The screen copy pass (running)** · **`og:title` on 2 routes but not 31** · **The stacked-Pip-voice line on the first setup screen** · **The early-approval seam** · **Onboarding screenshots for screen 3** · **Quick Add default EXPANDED on empty board** · **Lists "5 OPEN · 348 DONE"** fossil counter · **The Briefing FAB overlapping the Campaigns bar** · **Pip help discoverability** · **Reward scarcity limits** · **Yearly/monthly event recurrence** · **Multi-day calendar events** · **Calendar alerts** · **Wall ticker speed** · **Wall calendar event-pill member color** · **"Forgot PIN" confirm() copy** · **`decisions.md` header "Status tiers" line missing SUPERSEDED**.

---

## 🅿️ PARKED

See `parking-lot.md`. **The offline shell** · **Forge's Option-B game** · **Endure as a native product** · **Living-hold theme packs — monetization SKU #2** · **QA #5 super-admin / tier-2 support role** · **Own-session vs per-member-auth** · **empty-roster-seat** · Favorites on the wall · role-label retirement ("Parent/Kid") · #8b admin-reporting · kid-vs-kid impersonation · kid-auth (declined) · photo avatars · cosmetic drop #2 · Capacitor (DECLINED) · flat/peer holds · scripted screenshot capture (DECLINED) · the "how Scott & jAIne work" collaboration profile · the timezone nudge · injury-prescription liability posture at stranger scale · commercial-gym equipment model.

---

## 🔴 WALK-UP TRUST BOUNDARY — recon on record

Switching into a PIN-less adult profile hard-gates correctly. The "active member" switch is cosmetic, not a security boundary — physical possession of an unlocked parent session = parent authority. In the shared-session model a device-kid holds the owner's ambient parent JWT. **This is intra-household, not cross-tenant** — `current_family_id()` derives server-side from `auth.uid()`. Accepted for household use. **Three findings are ignored or downgraded *because of* this boundary.** Deciding the own-session-vs-per-member-auth fork revives all three. The fork is parked.

⚠️ **`useActiveMember()` reads the switched-to profile's own `role` column client-side; every RPC and RLS policy evaluates `auth.uid()`, which is always the owner. The client thinks a kid is acting; the database always thinks the owner is.** **Three RPCs now take a validated actor/profile id precisely to bridge it** — `approve_redemption`, `wall_request_redemption`, `mark_first_run_complete`.

---

## 🔵 THE BUILD MODEL — holding

- **A REPUBLISH IS CHEAPER THAN A THEORY. (NEW — 2026-08-01 late.)** `/sw.js` 404'd while its directory-mates served. jAIne proposed a Nitro static-output theory, then a host-reserves-the-path theory. **Scott republished and it resolved.** **When a file is in the repo, in the deploy, and absent from the origin, publish again BEFORE building a model of why.**
- **A TRACKING ITEM WITH NO EVIDENCE AND NO DATE IS A RUMOR. (NEW — 2026-08-01 late.)** Three carried items died on contact tonight; two had never described anything real. **"One look" made them cheap to defer rather than cheap to kill. An item that survives on cheapness should be dated so its age is visible.**
- **A RECON ANSWERS THE QUESTION IT WAS BRIEFED WITH. (NEW — 2026-08-01 late.)** The 08-01 recon checked five verbs and reported them identical. jAIne generalized to the whole switch and folded "word-for-word identical" into canon. **`event_created` had already drifted. Do not generalize a finding to the container the question lived in.**
- **A STOP-CLAUSE IN A BRIEF IS WORTH MORE THAN A CORRECT INSTRUCTION. (NEW — 2026-08-01 late.)** The blurb brief said delete, and added *if this turns out to be an empty state, stop and report.* **It was an empty state. The clause caught jAIne's wrong call before it shipped.** Cheap to write, and it converts a wrong brief into a finding.
- **THE FIRST VERSION OF A GUARD CAN CONTAIN THE RACE IT WAS MEANT TO PREVENT. (NEW — 2026-08-01 late.)** jAIne specified a `window.load` guard for SW registration. **If `load` has already fired at hydration, the listener never runs.** Passes on a cold load, fails on a warm one. **jAIne's brief, jAIne's defect.**
- **A PLAN ITERATION COSTS A CREDIT.** **Review a plan in ONE pass.**
- **AN INVARIANT MUST BE AN EXPLICIT LINE IN THE BRIEF, NOT INFERRED FROM PROSE.** ✅ **Worked tonight: the service worker brief listed six numbered pass/fail invariants and all six held.**
- **JAINE'S PUNCTUATION REACHES THE GLASS THE SAME WAY HER RATIONALE DOES.** ⚠️ **And the sweep that removes it can introduce a new defect: `laundry,it` lost its space to the em-dash pass and shipped.**
- **A CANONICAL SNAPSHOT CAN BE CONSUMED BY REAL LIFE.**
- **READ THE WHOLE MESSAGE BEFORE ANSWERING IT.**
- **ANSWER THE QUESTION ACTUALLY ASKED, AT THE REGISTER IT WAS ASKED.** ⚠️ **New instance: asked for the answer, jAIne gave the answer and then appended two more instructions before executing.**
- **TWO CANON DOCS CAN CONTRADICT EACH OTHER AND YOU WILL READ BOTH.** **The one describing SHIPPED BEHAVIOR wins.**
- **ASK ONE QUESTION BEFORE WRITING A FINDING.**
- **LENGTH IS A DEFECT WHEN IT OUTRUNS THE READER.**
- **NEVER-WORKED AND BROKE LOOK IDENTICAL FROM THE GLASS.**
- **A RECON'S FORENSICS AND ITS RECOMMENDATION ARE SEPARATE ARTIFACTS.**
- **GREP CANNOT FIND A BUG THAT ISN'T A STRING.**
- **BRIEF THE RECON TO DISPROVE.**
- **A SUBTITLE EXPLAINING SOMETHING VISIBLE IS BRIEF TEXT THAT ESCAPED ONTO THE GLASS.** **Default is deletion, not rewording.** ⚠️ **Unless the surface is empty — then the copy is the only thing there and deletion is wrong.**
- **A FLAGGED LINE IS A BETTER OUTCOME THAN A GUESSED ONE.**
- **REACH FOR THE SIMPLE EXPLANATION BEFORE THE DEFECT.** ⚠️ **Walked past twice in one exchange tonight.**
- **A FILE-BY-FILE SWEEP CAN STILL UNDER-READ A FILE IT VISITED.**
- **CODE IS ZERO LOVABLE CREDITS — MEASURED, NOT ASSUMED.** ✅ **Five jobs, one session, zero credits.**
- **A RULE THAT PERMITS TWO WORDS FOR ONE OBJECT IS NOT A DECISION.**
- **A CORRELATION IN A SCREENSHOT IS A HYPOTHESIS.** ⚠️ **And a LABEL in a screenshot is not necessarily on the surface at all — the "Done today" header never existed.**
- **FIX THE CONTAINER, NOT THE CONTENT.**
- **USER-AUTHORED CONTENT IS OUT OF SCOPE FOR VOCABULARY AUDITS.**
- **REVERTIBILITY IS A LANE CRITERION, NOT JUST CREDITS.**
- **A 🔴 WITH NO CONSUMER IS NOT A 🔴.**
- **AN ADULT PROFILE ID IS NOT ALWAYS A USER ID.** ⚠️ **Three shipped instances. The defining bug class of the codebase.**
- **A DEFAULTED PARAMETER DOES NOT REPLACE A FUNCTION.**
- **WHEN THERE IS NO GLASS, VERIFY THE DATA.**
- **TWO BUGS THAT CANCEL ARE WORSE THAN ONE THAT SHOWS.**
- **SWEEP THE CLASS ONLY WHEN IT IS ONE.**
- **"SYNCS TO `origin/main` BEFORE READING" IS NOT SELF-ENFORCING.**
- **NAME EVERY CONSUMER OF A ROUTE BEFORE REMOVING IT.** ⚠️ **And ask whether YOU are one.**
- **A CLAIM ABOUT CODE IS NOT VERIFIED BY THE AGENT'S SUMMARY OF IT.**
- **RLS AND GRANTS ARE TWO GATES, NOT ONE.**
- **FIX THE MECHANISM, NOT THE INSTANCE.** · **BUILD THE FRAME BEFORE THE CONTENT.** · **DECOMPOSE BEFORE YOU PROMOTE.**
- **Fetch the canon before producing anything.** ⚠️ **And verify the file, not the tracking item that describes it.**
- **A code revert is not a database revert.**
- **A Code job isn't done until the artifact is observable from outside the agent.**
- **HTTP 200 is not "renders."** ⚠️ **And a file rendering in the address bar is not the same request the browser makes for it — the SW fetch carries different headers and got a different answer.**
- **Model routing:** Haiku (mechanical) · Sonnet (build, diagnosis, synthesis recon) · **Opus (tenant-isolation audit, and the jAIne seat).**
- **One writer at a time.** · **SESSION LANE, DECLARED AT OPEN.**

---

## ✅ EARLIER — SHIPPED (compressed; git owns the detail)

- **2026-08-01 (late)** — the free session. Service worker shipped for installability; master-spec fold done from a read; page titles to middot; Slate collapsed-group label; `member_admitted`/`member_denied` rendered. Three carried items closed by looking. Zero credits, five Code jobs. `e813dca` → `b2efd8f`.
- **2026-08-01 (early)** — the marker session. First-run marker read/write fixed via a shared resolver; `verbLabel` scoped and closed without a fix; copy pass batch two plus a 125-character em-dash sweep. **August 1 roll-forward passed both legs.** ~2.8 credits. `eb93e73` → `fd1d6a8`.
- **2026-07-31** — the redemption-path session. Vault kid-redemption fixed by routing to the existing wall RPC; wall approvals now log; the Slate copy pass. Four Code jobs, zero credits. `e186ff0` → `1ec7af2`.
- **2026-07-30 (night)** — the Slate + the Ledger. Roll-forward for all three cadences, same-row. `retired_at`. Five credits.
- **2026-07-30 (late)** — QA/design session. Bounty supersedes Quest; the Slate and the Ledger; the roll-forward rule.
- **2026-07-30 (early)** — the redemption `decided_by` fix, verified on live data.
- **2026-07-29 (late)** — the master-spec fold + the first-run completion marker + `943a633`.
- **2026-07-29 (early)** — the install tutorial.
- **2026-07-28** — Forge design session, zero code.
- **2026-07-27 (late)** — the non-creator first run.
- **2026-07-27 (early/mid)** — the `families.timezone` update path.
- **2026-07-26** — table grants closed. Five-screen Pip-guided first run. Signup rebuilt.
- **2026-07-25** — the constitution restructure; the module reframe.
- **2026-07-23** — the household-local date seam.
- **2026-07-21** — the SECURITY DEFINER grant surface; `isActiveQuest` unified.
- **2026-07-19** — P4×L8 tenant-isolation audit RUN, BREACHED, FIXED, VERIFIED.
- **2026-07-16 → 06-26** — roster fixes, admit-on-approval, Claude Code proven as a build lane, avatar roster, XP killed, Vault rails, Lists v1, Campaigns, Calendar, PIN, Quest Log.

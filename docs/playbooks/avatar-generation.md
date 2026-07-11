# Playbook — Avatar Generation

How to generate on-band Emberhold avatars. **This is hard-won and it is not obvious. Read it before touching a generator.**

The *style floor* (what an avatar must be) lives in `master-spec.md` → Avatars. This doc is the *method* (how to get one).

Validated engine: **Microsoft Copilot Create** (work account — zero Lovable credit burn). The method should transfer, but recalibrate on any new engine before trusting it.

---

## Rule 1 — The style block controls output by AXIS, not by LENGTH

This is the whole game, and it's counterintuitive.

**PIN:** the floor (luminosity / painterly / jewel-color) and the near-black background.
**FREE, explicitly:** casting, palette, and pose.

| Failure mode | What went wrong |
|---|---|
| **Minimalist block** → eight identical green three-quarter portraits | It *accidentally pinned* palette and pose by leaving them unspecified — the engine defaulted, and defaults are uniform |
| **Over-long block** → jAIne's characters, not the engine's | It *pinned casting* — writing the cast list is grabbing the generator's wheel |

**The correct block is: short on casting, firm on floor, explicit variety instruction on palette/pose/build.**

Both failures are **axis errors, not length errors.** Naming the axes is what cracked it.

---

## Rule 2 — Generate the FULL roster in ONE run

With a cross-roster anti-repeat constraint: *"no duplicate archetypes across all N."*

**That constraint can only bind when the engine sees the whole cast at once.** Class-by-class briefing recycles the same ~6 archetypes, because each class is briefed blind and can't dodge repeats it can't see.

One run beats four sittings. This held across a 40-character generation: floor intact across four pages, no archetype doppelgangers.

**Name the archetype traps explicitly**, as *"at most once in the entire roster"*:
- silver-locs elder
- curly-haired beauty
- big-eyed elf child
- generic young-hero man

---

## Rule 3 — The cool/eerie register runs on MATERIAL and LIGHT, never lore

Content filters flag the entire supernatural semantic field **by noun density** — and they **ignore** "family-friendly / not horror" disclaimers entirely.

**Blocked vocabulary:** undead · lich · revenant · wraith · ghost · spectral · spirit · cursed · glowing-eyes · emerging-from-shadow

**The same eerie image is reachable through physics:**
- cold rim-light
- one-sided lighting into deep shadow
- frost armor, cracked stone, cold metal
- colored glow-accents on armor

Describe the *materials* and the *light*. The mood follows. Prerequisite discipline for any eerie-cool or older-kid asset work.

---

## Rule 4 — Circular-crop-safe framing (permanent composition rule)

Every avatar composed **centered, with even margin on all sides.** Full head — crown, horns, hat included — plus shoulders sit **inside a centered circle with breathing room.** Nothing touching edges.

Fixes the crop biting crowns and shoulders. **Applies to all future avatar generation regardless of engine.**

---

## Rule 5 — Committed range per class

Not "cute where the class allows." **Every class spans cute / majestic / COOL**, and carries **2–3 genuine kid-magnets.**

Generators **underweight the cute end by default** and regress to one token cute character per class unless explicitly pushed. Push.

Why this matters more than it sounds: which avatars the kids actually react to *is the product thesis*. The delight layer isn't decoration.

The cool-register picks that completed the roster: Forge → ember-lava warrior-woman · Garden → antlered stag-warden · Keep → crystalline ice knight · Hall → twilight crown-queen. Note each matches **its own class palette** — that's what dissolved the "all eight are the same blue" cohesion problem.

---

## Rule 6 — Fence the leaks

Two things bleed if you don't explicitly stop them:

- **The luminosity constraint leaks into the background** and sets everything on fire. Backgrounds derive from *that character* (element / domain / mood), kept dark enough to blend into `#1A110B` in a circular crop. **Filled portrait, not a transparent cutout.** No uniform gold frame, no shared set-dressing — the rim-light unifies the roster, a frame does not.
- **Humanoids default to one young light-skinned face.** Fence it explicitly. (May caught this; she was right.)

---

## Rule 7 — The reference-sheet technique (how to extend the roster forever)

Hand the generator **the existing roster sheet as a style reference.** It holds **FORMAT** — re-pins bust-up portrait framing, out-muscles splash-card drift — far better than any prose rule. The prompt is then free to steer **PALETTE** and register.

**You must say explicitly:** *"style reference only — do not edit or redraw the sheet; make all-new characters."* Otherwise it will rework the sheet instead of extending it.

This is the cleanest way to grow the roster indefinitely: point at the set, say *"more like this, but [shift]."*

---

## Asset hygiene

Image generators **always lie about "transparent background"** — they paint a box or a checkerboard and call it transparent.

Sidestep it entirely: **default to filled-dark backgrounds** that blend into `#1A110B`.

For any genuine cutout need: generate on **flat pure-green**, chroma-key, and **verify alpha programmatically.** Never trust the claim.

---

## The lane rule, restated

Scott's favorite avatars came from **loose briefs where the engine authored unsupervised.** The over-specified generations were the weakest.

**Brief the floor + the intent. Leave latitude on execution.** Don't throttle a Ferrari to 20mph.

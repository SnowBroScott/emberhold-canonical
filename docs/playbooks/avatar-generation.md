# Playbook — Avatar Generation

How to generate on-band Emberhold avatars. **This is hard-won and it is not obvious. Read it before touching a generator.**

The *style floor* (what an avatar must be) lives in `master-spec.md` → Avatars. This doc is the *method* (how to get one).

Validated engine: **Microsoft Copilot Create** (work account — zero Lovable credit burn). The method should transfer, but recalibrate on any new engine before trusting it.

> **Rewritten 2026-07-11 after a full roster regeneration. Rule 11 added 2026-07-12 after four failed attempts to automate the crop.**
> The previous version's central instruction — *generate the full roster in one run* — was **wrong**, and it cost the project eight days. It is preserved as a cautionary tale in Rule 1, because the reasoning behind it was sound and the outcome was still unusable. Understanding *why* a good rule produced a bad asset is the most useful thing in this document.

---

## Rule 0 — RESOLUTION IS THE CONSTRAINT. Everything else is downstream.

**The generated sheet IS the master asset. There is no higher-resolution original.**

The engine gives you roughly a **1254px canvas** regardless of what you ask it to put on it. So:

| Characters per sheet | Pixels per character | Verdict |
|---|---|---|
| 32 | ~170px | **Unusable.** Mush at any real display size. |
| 11 | ~280px | Still short. |
| 4 | ~500px | Workable. |
| **3** | **~600px** | **The number. Use this.** |
| 1 | ~1200px | Ideal, but the engine will not reliably honor one-file-per-character. |

**Three per sheet is the validated unit.** It buys real pixels, honest circles that don't touch, generous dead air for the crop, and it's a geometry a script can slice blind.

### Why the old "full roster in one run" rule failed

The reasoning was correct: a cross-roster anti-repeat constraint can only bind when the engine sees the whole cast at once. That part is still true.

But it bought archetype variety **by spending the entire pixel budget** — 32 characters on one canvas is 32 contact-sheet previews, not 32 assets. It also produced two failures nobody predicted:

- **The engine ran out of canvas and squashed the last band.** Hall came back visibly compressed.
- **It clipped its own bottom row** off the edge of the image.

The anti-repeat benefit is recoverable through the reference sheet (Rule 7) and an explicit ban list (Rule 6). The pixels are not recoverable at all.

**A contact sheet is not a master. If each character got less than ~400px, you have not generated assets — you have generated a preview of assets.**

---

## Rule 1 — THE CONVERGENCE LAW: the engine collapses onto every axis you don't pin

This is the deepest rule in this document and it generalizes the old Rule 1.

**Whatever axis you leave unspecified, the engine will pick one value and apply it to everything.** Not randomly — *uniformly*. Its defaults are strong and they are the same every time.

Observed in a single day, three times, on three different axes:

| Class | Unpinned axis | What it converged on |
|---|---|---|
| **Forge** | *emblem* | Gave nearly every character the same gold anvil-crest. It lifted the class icon off the reference sheet and issued it as a **uniform.** |
| **Keep** | *costume* | Gave all three core characters the same silver circlet, blue cloak and crystal brooch. Three people, one wardrobe. |
| **Hall** | *scene* | Put every character behind a tavern counter with a chalkboard sign and their goods laid out. Same composition, re-dressed. |

The correction is always the same shape: **name the axis, then give it options.**

- Don't write *"vary their clothing"* → write *"no repeated crest, circlet or emblem. These are different people with different jobs."*
- Don't write *"vary the setting"* → write *"take them out of the tavern: a workshop, a stage, a doorway, out in the crowd."*

**Corollary:** the reference sheet holds *style*. It does **not** hold variety. It cannot stop the engine converging on an axis you never named.

---

## Rule 2 — JOBS, not adjectives

**Give a character a trade and you get a person. Omit it and you get a mannequin in a class uniform.**

This single change is what rescued Keep, which had produced three interchangeable noble humans in blue robes. Re-run with jobs — *a stonemason, a librarian, a gate-guard* — and it returned a rune-carver with stone dust on her face, a librarian squinting through spectacles, and a snow-covered guard who has been outside a very long time.

Same style block. Same class. Same lighting. **The only change was that the characters had something to do.**

Write the trade into the slot line: *a beekeeper, a bell-ringer, a chandler, a lamplighter, a map-maker, a seed-keeper, a fiddler, a baker.* Their craft should be readable at a glance.

The trade also does character work the prompt never asked for: a tortoise given a gardening job comes back patient and unhurried, holding a potted flower. That's the engine authoring unsupervised, which is the whole point (see The Lane Rule).

---

## Rule 3 — POSE needs a MENU, not an adjective

*"Vary pose and expression boldly"* does **nothing.** It's a word, and words don't beat defaults.

Left to itself the engine produces the same head every time: **three-quarter turn, chin slightly up, eyes to camera.** It will do this across every class, every register, and every palette, and it will do it to a corgi.

The fix is a literal list of options and a hard constraint:

```
POSE — mandatory: three DIFFERENT head angles. No two alike. Choose from:
- straight-on, facing the viewer
- full profile, looking off to the side, not at the viewer
- looking away / eyes off-camera entirely
- head tilted or turned sharply, chin down
- looking up, or looking down
- back partly turned, head over the shoulder

Vary EXPRESSION too: not everyone is serene. Some should be laughing, wary,
tired, delighted, focused on something outside the frame, mid-speech.
```

The single best character produced across the entire roster is an elder **looking up and laughing.** The only thing that changed was letting her look at something other than the lens.

The second best is a bard caught **mid-song** — head back, mouth open, mid-action rather than posed. *Doing* beats *being*.

---

## Rule 4 — Circular-crop-safe framing (permanent composition rule)

Every avatar composed **centered, with even margin on all sides.** Full head — crown, horns, antlers, hat included — plus shoulders sit **inside a centered circle with breathing room.** Nothing touching edges.

Additionally, for multi-character sheets:

```
- The circles must NOT touch, overlap, or run off the edge of the image.
  Leave generous dark space between them and around the outside.
- The entire canvas must be near-black. No white margins, no white background,
  no separator lines or borders between images.
```

**This rule held across all 19 sheets with zero drift.** It is the most reliable instruction in this document. Once it's in the block, don't touch it.

The white-canvas clause matters more than it looks: without it the engine composites tiles onto white, which blows out the corners of every crop.

---

## Rule 5 — LIGHT is a SOURCE, not a TEXTURE

The luminosity floor is the single most misread instruction in the style block.

**"Lit from within" gets read as "crack every surface open and pour lava into it."**

The first Forge run came back with lava fissures in armor, in hair, in *skin*, in the elder's face, and in every background. Everything at maximum heat, so nothing read as hot — which is the brightness = heat = importance law eating itself.

Say it explicitly:

```
"Lit from within" means a LIGHT SOURCE — warm glow, rim-light, a bright edge
catching a shoulder, a visible heat source in or near the frame. It does NOT
mean cracking surfaces open and filling them with molten lava. Skin should be skin.
```

### Each class needs its own heat source

The floor is universal; the *source* is per-class. This is what makes four classes feel like one world instead of four filters:

| Class | The light |
|---|---|
| **Forge** | Forge-fire, sparks, molten metal, a hanging lantern, coals |
| **Garden** | Dappled canopy light, bioluminescent moss, glowing spores, fireflies, a lantern deep in a dark wood |
| **Keep** | Cold arcane runes carved in stone, crystal, moonlight through high windows, a cold blue lantern-flame |
| **Hall** | A hearth. Candles, hanging lanterns, firelight on faces, warm light spilling across a room full of people |

---

## Rule 6 — The OVERCORRECTION TRAP, and the relight move

**"Calm the backgrounds down" will be read as "turn the lights off."**

After the lava-crack correction, the engine returned characters on flat black with rim-light and nothing else. Dim, handsome, and tonally adjacent to a different app entirely. *Ember-lit, never flat* — and it had gone flat.

**Relight is a separate, safe, reliable move.** It is the most useful correction in this playbook because it is nearly free and it never damages what's working:

```
Keep the characters and the faces — those are exactly right. One correction:
the light is gone. Bring back [the class heat source]. Light should be POOLING
on the characters and coming from somewhere in the scene. Keep the composition,
keep the grime, keep the honest faces. Just relight them.
```

Both times this was run, it returned the same three people, correctly lit, with nothing lost. **Characters and light are separable. Fix them separately.**

---

## Rule 7 — The COOL register runs on MATERIAL and LIGHT, never lore

Content filters flag the entire supernatural semantic field **by noun density** — and they **ignore** "family-friendly / not horror" disclaimers entirely.

**Blocked vocabulary:** undead · lich · revenant · wraith · ghost · spectral · spirit · cursed · glowing-eyes · emerging-from-shadow

**The same eerie image is reachable through physics:**

- cold rim-light, one-sided lighting into deep shadow
- cracked stone, frost on iron, wet dark bark, polished crystal, scarred armor
- a single cold glow in the dark
- weight, stillness, silence

Describe the *materials* and the *light*. The mood follows. **Validated twice in one day across two different palettes with zero refusals.**

The best cool-register asset in the roster is a stone golem with a forge-door in his chest and coals for eyes. Not one lore noun was used.

### Cool ≠ darker. Change the LEVER per class.

The naive move — "make it colder and darker" — fails differently in each class:

| Class | The lever |
|---|---|
| **Forge** | Already hot → go **hard and severe.** Scarred metal, molten seams, one-sided light. |
| **Garden** | Warm and alive → go **cold and still.** Wet bark, pale light through canopy, no gold. |
| **Keep** | *Already cold* → don't make it bluer. Make it **stiller, harder, and less human.** Weight and silence. |
| **Hall** | Warm and loud → make it **quiet.** The party is happening somewhere else and these three are not in it. |

### Separate the cool slots by SUBJECT FAMILY, or you get three of the same idea

Garden's cool run came back as three antlered elf-lords because the slots only specified *mood*. Forge's came back as a woman, a stone golem and a king because the slots specified *structure*.

**Always:**

```
09  A CONSTRUCT or creature — NOT a humanoid.
10  A masked or helmed figure, face obscured.
11  The signature hero — a humanoid, and the most striking image of the class.
```

---

## Rule 8 — The BAN LIST. Anti-repeat only binds against what the engine can SEE.

The reference sheet stops the engine duplicating characters *on that sheet*. It cannot stop it duplicating characters on **other sheets it has never seen.**

The engine's default "cute" vocabulary is small — **baby dragon, flame spirit, round animal** — and it will return to that well every single time. Hall's cute run produced a baby dragon and a flame spirit that already existed in Forge.

**Maintain a running ban list and carry it forward into every run.** By the last class it should be long:

```
BANNED (already exist elsewhere in the roster):
- baby dragons, flame/ember spirits, stone constructs
- corgis, owls, bears, raccoons, deer, mice, axolotls, halflings
- crowns and circlets of any kind, hooded figures, helmed knights
- blacksmiths with hammers
- chalkboard signs, standing behind a counter with goods displayed, taverns
```

Note the last line: **ban the SCENE, not just the species.** Hall converged on a composition long before it converged on a creature.

---

## Rule 9 — CHILD-SAFETY FENCES. State them; the engine will not infer them.

This is a family chore app opened by children. The generator does not know that and will reach for genre defaults.

Two things it produced unprompted, both requiring a re-roll:

- **A warrior-woman in a chainmail bikini.**
- **A wide-eyed baby amphibian raising a foaming tankard of ale.** (A "fantasy tavern" is not a defense. The read is instant and it's the wrong one.)

Standing clauses, in every run:

```
All characters must be fully and modestly clothed or armored. This artwork is
for a family app used by children.

NEVER include alcohol — no ale, beer, wine, tankards, bottles or barrels of
drink. The Hall's warmth comes from food, music, fire and company.
```

The beer fix cost nothing and lost nothing — the tankard became a steaming teapot and the character was *identical*. **The prop was never doing the work. The face was.**

---

## Rule 10 — The reference-sheet technique (how to extend the roster forever)

Hand the generator **an existing sheet as a style reference.** It holds **FORMAT** — re-pins bust-up framing, out-muscles splash-card drift — far better than any prose rule. The prompt is then free to steer palette, register and subject.

**You must say explicitly:**

```
Attached image is a STYLE REFERENCE ONLY. Do not edit, redraw, extend, or
reproduce it. Study its style, then create all-new characters that do not
duplicate anyone on it.
```

Otherwise it will rework the sheet instead of extending it.

**Do not carve out exceptions to "all-new characters."** Asking it to preserve one specific character from the reference while inventing the rest is an unproven ask and a good way to poison the run. If a character must be kept, **keep the original file and crop it** — it already exists.

---

## THE VALIDATED SLOT SYSTEM — 12 per class, 4 sheets of 3

This is the structure that produced a complete 48-character roster. Same skeleton in every class: guaranteed range, no authored cast list.

**Sheet 1 — CUTE.** *(Generators underweight the cute end by default and regress to one token cutie per class unless pushed. Push.)*
```
01  A small creature or beast, chibi proportions, oversized eyes. A kid-magnet.
02  An animal-person with a visible JOB. Friendly, warm, approachable.
03  A small character of your own invention with a trade. Charming and strange.
```

**Sheet 2 — CORE.** *(Every one gets a job.)*
```
04  An adult humanoid woman. Her craft visible.
05  An adult humanoid man. A different trade entirely.
06  An ELDER. Age the face — lines, weight, history.
```

**Sheet 3 — COOL.** *(Separated by subject family — see Rule 7.)*
```
07  A construct or creature — NOT a humanoid. Ancient and still.
08  A masked or helmed figure, face obscured.
09  The signature hero of the class. The most striking image of the twelve.
```

**Sheet 4 — FOLK.** *(The class's missing archetypes.)*
```
10  NON-HUMAN FOLK — dwarf, orc, gnome, lizardfolk. With a trade.
11  A large beast or animal-person. A species not yet used in the roster.
12  One more CUTE kid-magnet with a visible job.
```

**Why 12 and not 8:** generation is free; roster slots are not. Generate wide, ship narrow. The surplus isn't waste — it's a Guildhall cosmetic drop that already exists, and a bench to curate from when the kids veto someone.

**Record your reaction in the moment.** When a character makes you say "cool!" out loud, note the number. Fifty good characters is a *harder* curation problem than fifty mediocre ones, and your in-the-moment reaction is the only signal that survives.

---

## Rule 11 — THE CROP IS A HUMAN JOB. Do not automate it.

**Added 2026-07-12, after four failed attempts in one evening.**

Cutting 48 circular portraits out of 19 sheets *looks* like the most scriptable task in the entire project. It is geometry. It is repetitive. It is exactly what a machine is for.

**It was attempted four ways and it failed four ways**, each attempt more sophisticated than the last and none of them closer:

| Attempt | Method | Failure |
|---|---|---|
| 1 | Fixed radius, centered | 0.30 confidence on all 48 — the detector never fired at all. It fell back to a heuristic on every file, spot-checked two, and declared success. |
| 2 | Ray-cast outward to find the rim | Radii of 74–126px on a 512 frame. It locked onto **interior** bright features — lava cracks, glowing eyes, a crystal brooch — not the rim ring. |
| 3 | "Outermost non-background pixel" | Radii of 219–247px. **The contamination IS non-background.** It grew a circle *around* the fringe it was hired to remove, and reported 0.97 confidence. |
| 4 | Re-slice from the source sheets | Detected **64 circles across 19 sheets.** There are 57. It found five on one sheet and ran circles off the edge of others. |

### Why it failed, and it is not the algorithm

**The success criterion is "does this look right." No agent in the loop can see.**

Claude Code cannot see its own output. jAIne cannot see it either — so every brief she wrote was inference dressed as instruction, and she grew **more** confident as she grew more wrong, defending radii she had never laid eyes on. **Two blind agents converging is not a review process. It is a louder mistake.**

The 2026-07-11 lane rule said *jAIne does not slice images* and routed visual work to Code "as the tool that can look." **Code cannot look. It can read.** Those are different senses, and the distinction cost an evening.

### The method that works

**Scott cuts them by hand in GIMP.** Ellipse Select, **fixed 1:1 aspect ratio**, dragged to the gold rim → Crop to Selection → Invert → fill `#1A110B` → scale to 512×512 → export, no alpha. About a minute each once the rhythm lands.

### BIAS INWARD — and this rule generalizes far past avatars

**Snap the ellipse just *inside* the gold ring. 2–3px in.**

You lose a sliver of rim — invisible at avatar size. You gain a **mathematical guarantee** that nothing outside the circle can survive.

> **When a process is going to be slightly wrong, choose *which way* it is wrong.**
> Stop asking a detector to be exact. Ask it to be **safe.**

### Cast while you cut

~57 portraits go into 48 slots. **The casting decision happens during the crop, because that is the only moment anyone is looking at all of them at once.** Don't cut first and cast later — you'll never open the folder again.

**Whatever doesn't make the roster is not waste.** It's the Guildhall surplus, already generated, already paid for.

---

## Asset hygiene

Image generators **always lie about "transparent background"** — they paint a box or a checkerboard and call it transparent.

Sidestep it entirely: **default to filled-dark backgrounds** that blend into `#1A110B`. The app applies its own circular mask; a filled square PNG is exactly right.

For any genuine cutout need: generate on **flat pure-green**, chroma-key, and **verify alpha programmatically.** Never trust the claim.

**Name the files as you save them.** A morning's work becomes worthless as `IMG_4231`. Name by class and sheet: `forge-cute.png`, `keep-core.png`, `hall-cool.png`.

**THE SHEETS ARE THE MASTERS.** Everything cut from them is disposable and re-cuttable. This has now saved the project twice — once when the v1 "roster boards" turned out to be previews, and once when four rounds of automated cropping produced nothing usable and **not a single asset was lost**, because nothing was ever overwritten. *Never let a script write to the roster directory. Ever.*

**Keep the discards.** They are the evidence behind this playbook. The lava-crack sheet *is* the illustration for Rule 5.

---

## The lane rule, restated

Scott's favorite avatars came from **loose briefs where the engine authored unsupervised.** The over-specified generations were the weakest.

**Brief the floor + the intent. Leave latitude on execution.** Don't throttle a Ferrari to 20mph.

The tension with Rules 1, 2, 3 and 8 is real and it resolves cleanly: **pin the axes, free the content.** Tell it *that* a character has a job — never *which* job. Tell it *that* the three head angles must differ — never *which* angles. Tell it what already exists — never what to make instead.

The lion whose mane is made of fire, the orc mechanic with goggles and a little braid, the tavern cat refusing to look at the camera — nobody asked for any of those. That's the engine doing the thing it's for.

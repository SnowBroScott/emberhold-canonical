# Avatar Assets

The Emberhold hero roster. **44 characters: 40 base (10 per class — Forge / Garden / Keep / Hall) + 4 cool-register (1 per class).**

Generated 2026-07-03 in Microsoft Copilot Create against the locked style floor. Method: `docs/playbooks/avatar-generation.md`. Style floor: `docs/master-spec.md` → Avatars.

---

## `roster-boards/`

The generation output, as delivered. **This is the backup of record** — before this commit, the roster existed only on Scott's phone.

| File | What it is |
|---|---|
| `roster-master-4-class-board.png` | The master board — all four classes, labeled |
| `cool-register-run.png` | The cool/battle-ready run. 8 generated across 4 class-native palettes; culled to 1 per class |
| `adorable-pass.png` | The targeted cute-end pass — proving every class can be cute *in its own material* |
| `adorable-pass-regen.png` | Regen detail from the adorable pass |

---

## What still has to happen — the transport pass

**These boards are contact sheets, not shipped assets.** The roster is **designed, not shipped** — and by the project's own standard, design-complete ≠ shipped.

The transport pass is the real remaining work:

1. **Crop** each character out of its board to an individual file
2. **Frame-check** against the circular-crop-safe rule — full head (crown / horns / hat) + shoulders inside a centered circle, nothing touching edges
3. **Name** on a convention (suggested: `forge-01-name.png` … `hall-10-name.png`, plus `forge-cool.png` etc.)
4. **Size / export** to whatever the picker wants
5. **Upload** to Supabase storage
6. **Slot** into the picker's per-class collapsible structure, including the 4 cool additions
7. **Split** free vs. paid — 16 free (4/class), 24 behind the Founding Guildhall. Each free four needs a genuine kid-magnet: *first-love is the free tier's job.*

Until step 7 is live in the app, the Founding Guildhall has nothing to sell.

---

## Rules

- **Never a bare photo in the circle.** A raw uploaded photo breaks the style floor and makes every hand-crafted portrait read as set dressing. The legitimate long-term answer to "I want it to be *me*" is a photo→stylized-hero pipeline that pushes a face *into* the floor.
- **Cosmetics are membrane-safe.** Avatars can be seen; they never affect gameplay, ember earning, or quest access. A paid avatar is not a better avatar.
- **Cosmetic unlocks are household-level. Always.** Per-class or per-character SKUs recreate a "buy each kid their skin" dynamic inside a household tool. One purchase, everyone gets everything. No exceptions.
- **Filled dark backgrounds, not transparent cutouts.** Generators lie about transparency. Backgrounds blend into `#1A110B`. If a real cutout is ever needed: generate on flat pure-green, chroma-key, verify alpha programmatically.

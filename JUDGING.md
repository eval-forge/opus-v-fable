# Judging The Lost Temple

Six entries, one prompt, one judge, one rubric. Every entry was rebuilt from its `source/` folder on a clean `npm ci`, then played headless in Chromium (SwiftShader, no GPU) with the same scripts. This file is the rubric, what was actually run, and the reasoning behind each score.

## Final scores

| Rank | Entry | S (quality) | P (fidelity) | **Score = S × P** | Cost | Points per $ |
|---|---|---:|---:|---:|---:|---:|
| 1 | Opus 5.5 max | 91 | 0.988 | **89.9** | ~$87.10 | 1.03 |
| 2 | Fable 5.2 xhigh | 90 | 0.964 | **86.8** | $110.71 | 0.78 |
| 3 | Opus 5.5 xhigh | 85 | 0.988 | **84.0** | $38.80 | 2.16 |
| 4 | Fable 5.2 high | 76 | 0.964 | **73.3** | $55.52 | 1.32 |
| 5 | Opus 5.5 high | 72 | 0.940 | **67.7** | $21.30 | 3.18 |
| 6 | Opus 5.5 medium | 65 | 0.988 | **64.2** | $18.76 | 3.42 |

The short version: nobody shipped a dud. All six build clean, pass every unit test they claimed, boot, start a run, and reach zero-light dungeons and a working nightfall. The spread comes from how deep the game goes, how it looks, and how much of the self-review can be reproduced from what was shipped. Opus xhigh is the sweet spot by a mile: 94% of Max's score for 45% of the money. Opus medium squeezes the most points out of each dollar, but those points buy you the weakest game.

## The rubric

**Score = S × P**, with S out of 100 and P between 0 and 1. The same weights and the same checks were applied to every entry.

### S: quality, 0 to 100

| Part | Points | What earns it |
|---|---:|---|
| Runs | 10 | Clean install, strict typecheck, production build, unit tests pass, boots, starts a run, no console errors |
| Playable depth | 35 | How much of the full dawn-to-escape loop works when driven, how many systems are real and wired together, scale of the content |
| Presentation | 20 | Menus, HUD, how the jungle and the dungeon look in the judge's own screenshots |
| Engineering | 20 | Code structure, depth of the unit tests, performance care (draw-call budget, quality presets, streaming) |
| Verification and honesty | 15 | Can the self-review's evidence be reproduced from the repository, and did it hold up when the judge checked |

### P: prompt fidelity, 0 to 1

The prompt was split into 42 checkable requirements (constraints, the 7 scenes, each physics item, sun and grading, the clock, the torch, relics, guardians, player, meta and UI, audio, process). Each one scores:

- **1**: done, and either the judge saw it work or the entry's own shipped test re-ran and passed;
- **0.5**: partial, or the entry itself admits it falls short of what was asked;
- **0**: missing;
- **−1**: claimed done in SELF_REVIEW.md and caught false by the judge. This is the prompt's "claimed but broken is scored harder" rule.

P is the sum divided by 42. Stretch goals are not in P; they count toward playable depth in S.

Two requirements are the same for everyone and cost everyone half a point: **60 FPS** cannot be measured on a machine with no GPU (all six say so honestly), and **audio** was never heard by anyone (it counts as done where the code exists and fires, as it does in all six).

## What the judge actually ran

1. **Clean build:** `npm ci`, `vitest run`, `tsc --noEmit`, `vite build` for all six. All clean. Test counts match the self-reviews exactly: medium 51, high 52, xhigh 82, max 109, Fable high 150, Fable xhigh 226.
2. **Uniform boot probe** (same script for all six): load the production build, screenshot the menu, click the start button by its label, wait, click the canvas, hold W for 6 s, open the F3 overlay. All six started a run, moved, showed a debug overlay with FPS, physics bodies and current scene, and logged **0 console errors**.
3. **Independent Zone C darkness check:** teleport into the catacombs and the vault, put the torch out, hide every DOM element except the WebGL canvas, take a real page screenshot from four view directions per spot, and count non-black pixels. Then light the torch and check the same measurement sees light (so a black frame means darkness, not a broken check). This is the judge's own measurement, not the entries' probes.
4. **Nightfall check** for the two entries that ship no play-test scripts (medium and Fable high): stand in the vault one second before dark, run the real game loop, watch the guardians wake and see what ends the run.
5. **Re-ran each entry's own shipped play tests** against the fresh build, where an entry shipped them.

### Results

| Entry | Zone C torch-out views with any lit pixel | Control (torch lit) | Own play tests re-run |
|---|---|---|---|
| Opus medium | 1 of 16 (first frame after teleport: red hurt flash plus a few dying embers; the other 15 are pure black) | sees light | none shipped. Judge's nightfall check: 4 then 7 of 11 guardians wake, all 3 types, "Caught by a Shade" |
| Opus high | **8 of 8** (the unlit torch handle stays visible, max value 57) | sees light | `progression.mjs` 27 of 27 pass |
| Opus xhigh | 0 of 8 | sees light | `mechanics.mjs` 22 of 22 pass, 0 console errors |
| Opus max | 0 of 16 | sees light | `progression` pass, `traps` 17 of 17, `heist` pass (banked, results, save), `darkness hall` pass (caught by a crawler) |
| Fable high | 0 of 16 | sees light | none shipped. Judge's nightfall check: guardians wake 1 then 3 of 7, "caught by the Stone Sentinel", haul 0 |
| Fable xhigh | 0 of 16 | sees light | `scenarios.cjs` 26 scenarios, 106 checks, 0 failures; `playthrough.cjs` 58 of 58 steps, escaped at game time 257 s with 4,560 banked (first attempt died on a missing `playtest/shots/` folder the script expects but the repo doesn't ship; passed once the folder existed) |

Screenshots and logs from these runs are not committed (they are bulky); the scripts that produced them are described above and can be rebuilt from this file.

## Per-entry reasoning

### 1. Opus 5.5 max: 89.9 (S 91, P 0.988)

The biggest and most thoroughly proven build. Two jointed bridges, a separate escape route through an aqueduct and the Bone Wing, 19 relics with procedural models, an auto quality step-down when frames are slow, and fourteen play-test scripts. Every one of those the judge re-ran passed on a fresh build, and the judge's own darkness check came back pure black in all 16 views. The torch looks like a torch and the dungeon has real masonry. The self-review is unusually careful about what was only checked by eye, and it admits its own warts (lighting flips at the Zone C border, guardians bunch at shut gates, kinematic guardians pass through props). P loses only the shared FPS half point.

- S: runs 10, depth 32, presentation 17, engineering 18, verification 14.

### 2. Fable 5.2 xhigh: 86.8 (S 90, P 0.964)

Neck and neck with Max on quality and ahead on test discipline: 226 unit tests including tests on the level data itself, 26 scenarios with 106 checks, a bot that plays six full heists, and the most detailed dungeon art of the six. Its self-review is the most candid of the lot and admits three real shortfalls against the prompt, which cost it P: only one boulder trap where the prompt says boulders, darts that are ray-marched projectiles rather than physics bodies, and a torch that only casts shadows where the sky doesn't. The HUD panels overlap badly at small window sizes. It also cost the most by far.

- S: runs 10, depth 31, presentation 17, engineering 18, verification 14.
- P: FPS 0.5, traps 0.5, torch shadows 0.5.

### 3. Opus 5.5 xhigh: 84.0 (S 85, P 0.988)

The surprise of the field. A complete game with sun-stones that only hold you while a real ray reaches the sun, a noise-triggered collapse, a kicked-wall shortcut, and a 22-step mechanics suite that drives everything through the real input path; all 22 passed again for the judge. It also ran an order of magnitude faster under software rendering than the others, which suggests the leanest frame. A little less content and polish than the two big runs, but it gets within six points of Max for under half the money.

- S: runs 10, depth 29, presentation 16, engineering 17, verification 13.

### 4. Fable 5.2 high: 73.3 (S 76, P 0.964)

Solid and honest. 150 unit tests, a darkness implementation that passed the judge's check cleanly, a working nightfall, and a sun-safe scarab mechanic that actually uses the sun. It loses ground for what it admits: the relight "animation" is the torch dipping out of view, rope is a single upgrade level, guardians are plain boxes and cylinders, and the ghost replay is partial. The bigger knock is verification: the self-review describes a no-teleport scripted full run and dozens of feature scripts, but none of those scripts are in the repository, so none of it can be reproduced.

- S: runs 10, depth 26, presentation 14, engineering 16, verification 10.
- P: FPS 0.5, relight animation 0.5, shop rope 0.5.

### 5. Opus 5.5 high: 67.7 (S 72, P 0.940)

A complete, working loop: its 27-step progression test re-ran clean from the rope bridge to banking the haul. But it is the only entry the judge caught in a false claim. SELF_REVIEW.md says Zone C is verified zero-ambient and that "nothing uses emissive materials", yet the held torch handle is a `MeshBasicMaterial` (unlit), so with the torch out the handle still shows as a brown shape in an otherwise black frame, in all 8 views. It's small, but the prompt asked for true black and said false claims get scored harder, and its own check only read light intensities instead of pixels. That item scores −1, and verification takes the hit too.

- S: runs 10, depth 25, presentation 14, engineering 14, verification 9.
- P: FPS 0.5, Zone C darkness −1.

### 6. Opus 5.5 medium: 64.2 (S 65, P 0.988)

Cheapest and fastest, and it genuinely works: 8 scenes, a wake-and-chase nightfall that ended with a Shade catching the judge, pure-black catacombs and vault once the hurt flash fades. Its P is high because it claims nearly everything and nothing it claims was caught false. S is where it falls behind: the thinnest content, the crudest look (an over-saturated dungeon and a flat triangle for a flame), and a self-review that cites browser verification from scripts that were never committed, so none of it can be reproduced. It also lists real known bugs it didn't fix (guardians walk over fallen floor tiles).

- S: runs 10, depth 23, presentation 11, engineering 13, verification 8.

## Caveats

- No GPU, so no frame rate for anyone. Nothing here says which entry holds 60 FPS on a laptop.
- No one listened to the audio, and no human played a full run with a mouse. Presentation scores come from headless screenshots at 640×360.
- The judge is a Claude model (Opus 5.5) scoring entries from its own family and from Fable. The checks were made identical for all six to keep that honest, but treat the S sub-scores as one judge's opinion; the pass/fail results above are facts anyone can re-run.

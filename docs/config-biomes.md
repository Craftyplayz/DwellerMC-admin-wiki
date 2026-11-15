# DwellerBiomes config

File: `plugins/DwellerBiomes/config.yml` (default: `DwellerBiomes/src/main/resources/config.yml`).

!!! danger "Never delete or rename a biome a world already uses"
    Chunks that use it break. Generated biome files are never deleted for this reason (`BiomePack.java`).

**When changes apply:** a full **restart** (not `/reload`): the biome datapack is built during bootstrap. Changes only affect **newly generated chunks**. A biome name that isn't loaded stops startup with `Biome ... is not loaded. Restart the server (not /reload) after adding biomes.`

!!! note "TODO: vanilla ores in vanilla-biome patches"
    The committed config still lets `lush_caves`, `dripstone_caves`, `sulfur_caves` and `deep_dark` run Minecraft's own ore features (coal, iron, gold, redstone, lapis, diamond, copper) on top of [DwellerCore ores](config-core.md#ores-new-chunks). A change that strips them (new per-biome key `remove-features`) is in progress but not committed yet. Update this page when it lands.

## Top-level keys

| Key | Default | Effect |
|---|---|---|
| `worlds` | `[world]` | Worlds that get the roof populator and biome restyling. The biome provider itself is used by any world whose generator asks for it (DwellerCore does). |
| `fallback-biome` | `caves` | Biome for cave cells outside every patch. Required. |
| `underground-features` | anchor `&underground` | Shared feature list reused by custom biomes (see below). Not read directly by the code; it's a YAML anchor. |

## roof

| Key | Default | Effect |
|---|---|---|
| `roof.biome` | `bedrock_roof` | Biome for every cell from the roof underside upward. Required. |
| `roof.base-y` | `0` | Reference Y of the roof. |
| `roof.underside-min` | `4` | Underside is always at least this far below `base-y`. |
| `roof.underside-depth` | `12` | Plus up to this much more from noise. With defaults the underside hangs to Y -16. |
| `roof.top-height` | `6` | Bumpy top rises up to this far above `base-y`. |
| `roof.scale` | `24` | Bump width in blocks; bigger = smoother. |

The roof biome starts at `base-y − underside-min − underside-depth` (Y -16 with defaults). Below the solid underside, a speckled fringe: 50% chance of bedrock 1 block down, 20% 2 blocks down. DwellerCore leaves Y -16..0 solid and air above, so keep the roof inside about Y -18..15.

## biomes.&lt;name&gt;

Names may only use `a-z`, `0-9`, `_`. Each entry becomes `dwellerbiomes:<name>`, unless `biome:` points at an existing biome.

| Key | Effect |
|---|---|
| `biome` | Optional. Use an existing biome (e.g. `minecraft:lush_caves`). No JSON is generated and `definition` is ignored. |
| `placement.min-y` / `max-y` | Depth range of the patches. Patches shrink within 32 blocks of either edge. |
| `placement.size` | Patch size in blocks (noise scale; vertical scale is 0.6× so patches are flatter than wide). Default 120. |
| `placement.rarity` | Noise threshold, default 0.35. Higher = rarer. Overlapping biomes: the one with the highest noise value wins. |
| `blocks.replace` | Only these blocks are restyled. Ores and anything else stay. Unknown block = startup error. |
| `blocks.layers` | Repeating horizontal bands, top to bottom, wobbling ±4 blocks. |
| `blocks.layer-height` | Band height (min 1, default 3). |
| `blocks.floor` / `floor-depth` | Block on cave floors, this many blocks deep (default 1). |
| `blocks.ceiling` | Block on cave ceilings. |
| `blocks.fill` | Optional. Every air block in the patch becomes this (e.g. `WATER` = flooded caves). |
| `decorations` | List of `{ block, on: floor/ceiling, chance }`. Chance per restyled block; first match wins. |
| `definition` | Copied as-is into the biome JSON (Minecraft 26.3 format): fog, sky, music, particles, mob spawns, `carvers`, `features`. Required unless `biome:` is set. |

Leave out `placement` for biomes that aren't placed in patches (roof, fallback).

### Default biomes

| Biome | Uses | Y range | size | rarity |
|---|---|---|---|---|
| `bedrock_roof` | generated | roof | – | – |
| `caves` | generated (fallback) | everywhere else | – | – |
| `sandstone_caves` | generated | -700 to -20 | 300 | 0.6 |
| `lush_caves` | `minecraft:lush_caves` | -500 to -40 | 260 | 0.6 |
| `dripstone_caves` | `minecraft:dripstone_caves` | -1000 to -150 | 300 | 0.6 |
| `sulfur_caves` | `minecraft:sulfur_caves` | -1600 to -700 | 280 | 0.6 |
| `deep_dark` | `minecraft:deep_dark` | -2000 to -1400 | 340 | 0.6 |

With these values about 86% of the world stays the `caves` fallback (measured by sampling the noise code).

## Feature order rule

`features` has 11 generation steps. A feature shared by several biomes must appear in the **same order in every biome**, or the server crashes with **"Feature order cycle"**. Easiest: reuse `*underground` and only add features at the end of a step.

## Adding a biome

1. Copy the `sandstone_caves` block under `biomes:` and rename it.
2. Change `placement`, `blocks`, `decorations`, `definition`.
3. If structures should spawn in it, add `dwellerbiomes:<name>` to their `extra-biomes` in [DwellerStructures](config-structures.md).
4. Restart.

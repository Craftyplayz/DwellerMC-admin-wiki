# DwellerBiomes config

File: `plugins/DwellerBiomes/config.yml` (default: `DwellerBiomes/src/main/resources/config.yml`).

!!! danger "Never delete or rename a biome a world already uses"
    Chunks that use it break. Generated biome files are never deleted for this reason (`BiomePack.java`).

**When changes apply:** a full **restart** (not `/reload`): the biome datapack is built during bootstrap. Changes only affect **newly generated chunks**.

**Bad values never stop the server.** A wrong type or out-of-range number logs a warning naming the key and the default is used. An unknown block, an unknown biome or a bad decoration row is skipped with a warning, and the rest of the biome still works. A whole cave biome is skipped (not placed) when its biome isn't loaded or its `min-y` is above `max-y`. Example:

```
[DwellerBiomes] Config roof.base-y: got '99', expected a whole number from -18 to 15. Using the default: 0
```

A skipped biome keeps the JSON file written on earlier starts, so chunks that already use it stay valid. The one setting the plugin can't run without is a roof and a fallback biome: if both the configured one and the default (`bedrock_roof`, `caves`) are missing, the plugin stops with `Biome ... is not loaded either`.

!!! note "No vanilla ores"
    `lush_caves`, `dripstone_caves`, `sulfur_caves` and `deep_dark` set `remove-features: *vanilla-ores`, so Minecraft's own ore features (17 listed under the top-level `vanilla-ores` anchor: coal, iron, gold, redstone, diamond, lapis, copper) are stripped. [DwellerCore ores](config-core.md#ores-new-chunks) are the only ore source. If a vanilla biome JSON can't be found in the server jar (wrong id, or a server update moved it), that biome keeps its vanilla ores in new chunks and the log says so: `Its features were NOT removed, so its vanilla ores generate in new chunks`. Check the log after every server update.

## Top-level keys

| Key | Default | Effect |
|---|---|---|
| `worlds` | `[world]` | Worlds that get the roof populator and biome restyling. Names are case-sensitive. The biome provider itself is used by any world whose generator asks for it (DwellerCore does). |
| `fallback-biome` | `caves` | Biome for cave cells outside every patch. A missing biome falls back to `caves`. |
| `vanilla-ores` | anchor `&vanilla-ores` | List of the 17 vanilla ore feature ids, reused by `remove-features`. Not read directly by the code. |
| `underground-features` | anchor `&underground` | Shared feature list reused by custom biomes (see below). Not read directly by the code; it's a YAML anchor. |

A world missing from `worlds` gets no roof at all: DwellerCore leaves the band Y -16 to 0 as plain stone, so players can mine up into the open air above Y 0. Add every DwellerCore world here.

## roof

| Key | Default | Range | Effect |
|---|---|---|---|
| `roof.biome` | `bedrock_roof` | biome | Biome for every cell from the roof underside upward. A missing biome falls back to `bedrock_roof`. |
| `roof.block` | `bedrock` | block id | What the roof is made of. An unknown block falls back to bedrock. |
| `roof.base-y` | `0` | -18 to 15 | Reference Y of the roof. |
| `roof.underside-min` | `4` | 0 to 33 | Underside is always at least this far below `base-y`. |
| `roof.underside-depth` | `12` | 0 to 33 | Plus up to this much more from noise. With defaults the underside hangs to Y -16. |
| `roof.top-height` | `6` | 0 to 33 | Bumpy top rises up to this far above `base-y`. |
| `roof.scale` | `24` | 1 to 1000 | Bump width in blocks; bigger = smoother. |
| `roof.spike-size-divisor` | `3` | 1 to 100 | Spikes on the underside come from a second noise `scale / 3` = 8 blocks wide. |
| `roof.underside-base-weight` | `0.75` | 0 to 2 | Underside depth = base noise x 0.75 + spike noise x `spike-weight`, capped at 1, times `underside-depth`. |
| `roof.spike-weight` | `0.4` | 0 to 2 | How far the spikes hang down. `0` = smooth underside. |
| `roof.fringe-chances` | `[0.5, 0.2]` | up to 8 numbers, 0 to 1 | Speckled fringe: chance per column of an extra roof block 1, 2... blocks below the solid underside (only where that block is rock). `[]` = clean underside. |

The roof biome starts at `base-y - underside-min - underside-depth` (Y -16 with defaults). DwellerCore leaves Y -16..0 solid and air above, and carves caves only below Y -17, so keep the roof inside about Y -18..15.

**What breaks.** An underside below Y -18 (for example `underside-depth: 20`) cuts bedrock into the top of the caves; above Y -16 it leaves a layer of normal stone under the roof that players can tunnel through sideways. `top-height` reaching past Y 15 is clipped by the world height. `roof.block` other than bedrock (for example `obsidian`) can be mined through, which gives players the open air above Y 0; [DwellerCore](config-core.md)'s burrower elites can't break bedrock, but they dig through anything up to `max-hardness`. Spike and fringe changes are cosmetic.

## patches

| Key | Default | Range | Effect |
|---|---|---|---|
| `patches.edge-fade` | `32` | 1 to 1000 | Within this many blocks of a biome's `min-y` or `max-y`, its patches shrink, so the edge isn't a flat cut. |
| `patches.flatness` | `0.6` | 0.05 to 20 | Patch height as a fraction of its width. `0.6` = flatter than wide, so patches follow cave layers. |

These apply to every placed biome. `edge-fade` larger than half a biome's Y range shrinks that biome everywhere (a range of 100 blocks with `edge-fade: 64` almost never reaches full strength). `flatness` above 1 gives tall, chimney-like patches. Both change how much of the world each biome covers; the player wiki biome finder uses `edge-fade` (`DW.biomeEdgeFade`), so update it there too.

## strata

| Key | Default | Range | Effect |
|---|---|---|---|
| `strata.wobble-scale` | `40` | 1 to 1000 | Distance over which the `layers` bands rise and fall, in blocks. |
| `strata.wobble-height` | `4` | 0 to 64 | How far the bands move up or down. `0` = ruler-flat layers. |

Only visible in biomes with `blocks.layers` (`sandstone_caves`, `dripstone_caves`, `sulfur_caves` by default). A wobble taller than `layers` x `layer-height` makes the bands look random rather than layered.

## biomes.&lt;name&gt;

Names may only use `a-z`, `0-9`, `_`; any other name is skipped with a warning. Each entry becomes `dwellerbiomes:<name>`, unless `biome:` points at an existing biome.

| Key | Default | Range | Effect |
|---|---|---|---|
| `biome` | own name | biome id | Optional. Use an existing biome (e.g. `minecraft:lush_caves`). `definition` is ignored. No JSON is generated unless `remove-features` is set. |
| `remove-features` | none | feature ids | Optional, only with a `minecraft:` `biome`. Feature ids removed from that vanilla biome; written as an override of the vanilla biome JSON in the datapack. Removing only, so it can't cause a feature order cycle. |
| `placement.min-y` / `max-y` | | -2032 to 15 | Depth range of the patches. `min-y` above `max-y` skips the biome. |
| `placement.size` | `120` | 1 to 100000 | Patch size in blocks (noise scale; vertical scale is `size x patches.flatness`). |
| `placement.rarity` | `0.35` | -1 to 1 | Noise threshold. Higher = rarer. Overlapping biomes: the one with the highest noise value wins. |
| `blocks.replace` | none | block ids | Only these blocks are restyled. Ores and anything else stay. Unknown blocks are skipped. |
| `blocks.layers` | none | block ids | Repeating horizontal bands, top to bottom, wobbling by `strata`. |
| `blocks.layer-height` | `3` | 1 to 1000 | Band height. |
| `blocks.floor` / `floor-depth` | none / `1` | block, 1 to 64 | Block on cave floors, this many blocks deep. |
| `blocks.ceiling` | none | block | Block on cave ceilings. |
| `blocks.fill` | none | block | Every air block in the patch becomes this (e.g. `WATER` = flooded caves). |
| `decorations` | none | rows | List of `{ block, on: floor/ceiling, chance }`, chance 0 to 1 per restyled block; first match wins. A row with an unknown block or bad chance is skipped. |
| `definition` | | | Copied as-is into the biome JSON (Minecraft 26.3 format): fog, sky, music, particles, mob spawns, `carvers`, `features`. Required unless `biome:` is set; without it the biome is skipped. |

Leave out `placement` for biomes that aren't placed in patches (roof, fallback).

**rarity.** `0` fills most of the depth range, `0.6` gives rare patches, `0.8` almost none. Below 0 a biome covers its whole range except near the edges. The default biomes all use 0.6. **size** under 50 makes small spotty patches that look like noise; above 1000 a patch can fill a whole region.

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

`features` has 11 generation steps. A feature shared by several biomes must appear in the **same order in every biome**, or the server crashes with **"Feature order cycle"**. Easiest: reuse `*underground` and only add features at the end of a step. This is a Minecraft check, so the plugin can't turn it into a warning.

## Adding a biome

1. Copy the `sandstone_caves` block under `biomes:` and rename it.
2. Change `placement`, `blocks`, `decorations`, `definition`.
3. If structures should spawn in it, add `dwellerbiomes:<name>` to their `extra-biomes` in [DwellerStructures](config-structures.md).
4. Restart. Check the log for `Config biomes.<name>` warnings.

See also: [DwellerCore config](config-core.md), [DwellerStructures config](config-structures.md), [Troubleshooting](troubleshooting.md).

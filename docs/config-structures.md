# DwellerStructures config

File: `plugins/DwellerStructures/structures.yml`. The first start writes it with every vanilla 26.3 structure and set at its current value (`DefaultConfig.java`).

**When changes apply:** restart. The datapack is rebuilt from scratch at every startup in `plugins/DwellerStructures/generated-datapack/`. Changes only affect newly generated chunks.

!!! info "Errors don't stop the server"
    Bad entries are logged and skipped. If the file can't be parsed at all, the plugin logs `Could not build the structure datapack; structures stay vanilla` and the server starts with vanilla structures. Exception: a biome id that doesn't exist inside a `biomes:` list can stop startup (only the format is checked).

## structures.&lt;name&gt;

| Key | Effect |
|---|---|
| `enabled` | `false` = never spawns (its biome list is emptied). |
| `biomes` | One `'#tag'` **or** a list of biome ids, not both. Mixing them keeps the default and logs a warning. |
| `extra-biomes` | Biome ids added to the structure's tag as **optional** entries, so a missing biome is skipped. Only works when `biomes` is a single `#tag`. |
| `y` | `default`, one number, or `[min, max]`. Only for jigsaw structures (marked `# y editable`). Sets a fixed Y range and `terrain_adaptation: beard_box`, which clears a room around the structure. Other types log a warning and ignore it. |

### DwellerMC defaults

Mineshafts, strongholds and trial chambers get every cave biome in `extra-biomes`: `dwellerbiomes:caves`, `dwellerbiomes:sandstone_caves`, `minecraft:lush_caves`, `minecraft:dripstone_caves`, `minecraft:sulfur_caves`, `minecraft:deep_dark`.

| Structure | extra-biomes | y | Set (spacing / separation / frequency) |
|---|---|---|---|
| `ancient_city` | `[]` (vanilla tag: Deep Dark only) | `[-1950, -1450]` | `ancient_cities`: 24 / 8 / 1.0 |
| `trial_chambers` | cave biomes | `[-1990, -640]` | `trial_chambers`: 34 / 12 / 1.0 |
| `mineshaft` | cave biomes | Minecraft's own, limited to Y -1000..-17 by DwellerCore | `mineshafts`: 1 / 0 / 0.008 |
| `stronghold` | cave biomes | Minecraft's own, limited to below Y -500 by DwellerCore | `strongholds`: 64 / 32 / 1.0 (spread over the world, not rings) |

Mineshaft and stronghold heights can't be set here: Minecraft places them at a random height below sea level. DwellerCore drops the ones outside its `structures.height-limits` (see [DwellerCore config](config-core.md#structures)), so their sets are tuned up to make up for the dropped ones. Mineshafts: about 45% of attempts land inside Y -1000..-17, so 0.008 gives roughly vanilla's 0.4% per chunk.

Every other structure keeps its vanilla biomes, so in a DwellerCore + DwellerBiomes world it doesn't spawn (those biomes don't exist there).

!!! warning "Roof rule"
    DwellerCore cancels any structure whose bounding box reaches **Y -16 or higher** (`StructureFilter.java`). Keep every `y` below -16. That's why `ancient_city` and `trial_chambers` default deeper than vanilla. Villages snap to the surface (the roof) and get cancelled, so underground villages don't work.

## sets.&lt;set&gt;

Structures listed in the same set share these numbers.

| Key | Valid range | Effect |
|---|---|---|
| `spacing` | 1–4096 | Average distance between spawn attempts, in chunks. |
| `separation` | 0 to spacing − 1 | Minimum distance between attempts, in chunks. |
| `frequency` | above 0, up to 1 | Chance each attempt spawns. |
| `distance` / `spread` / `count` | 0–1023 / 0–1023 / 1–4095 | Strongholds only: rings around 0,0. Ignored when the set has `spacing`, which switches strongholds to spread placement (`spacing` needs `separation` too). |

Out-of-range values: the whole set is skipped with a warning and stays vanilla.

## custom.&lt;name&gt;

Your own structure from an `.nbt` file saved with a structure block, copied into `plugins/DwellerStructures/custom/`.

| Key | Default | Effect |
|---|---|---|
| `file` | `<name>.nbt` | File inside `custom/`. Paths outside that folder are rejected. |
| `enabled` | `true` | `false` = skipped. |
| `biomes` | `['#minecraft:is_overworld']` | Ids or tags, written as optional entries (a missing biome never breaks startup). In a DwellerBiomes world, list the cave biomes. |
| `y` | **required** | One number or `[min, max]`. Missing = skipped. |
| `spacing` / `separation` / `frequency` | 32 / 8 / 1.0 | Same rules as sets. |

Names: lowercase letters, digits, `_`. Custom structures are single-piece jigsaw structures (`underground_structures` step) with `beard_box` room clearing.

See also: [DwellerBiomes biome names](config-biomes.md#default-biomes), [Troubleshooting](troubleshooting.md).

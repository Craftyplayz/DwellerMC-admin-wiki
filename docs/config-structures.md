# DwellerStructures config

File: `plugins/DwellerStructures/structures.yml`. The first start writes it with every vanilla 26.3 structure and set at its current value (`DefaultConfig.java`), plus a header that explains every key with its range. Values DwellerMC changes from vanilla carry a `# DwellerMC:` comment with the vanilla value next to them.

**When changes apply:** restart. The datapack is rebuilt from scratch at every startup in `plugins/DwellerStructures/generated-datapack/`. Changes only affect newly generated chunks.

!!! info "Errors don't stop the server"
    Bad entries are logged and skipped; that structure or set stays vanilla. If the file can't be parsed at all, the plugin logs `Could not build the structure datapack; structures stay vanilla` and the server starts with vanilla structures. A biome that doesn't exist in a `biomes:` list is skipped too, because lists are written as a tag of optional entries.

!!! note "Existing files keep their old header"
    The file is only written once. An older `structures.yml` keeps working (every new key is optional), but it doesn't get the new comments. Rename it and restart to see the current header, then copy your changes across.

## structures.&lt;name&gt;

| Key | Default | Range | Effect |
|---|---|---|---|
| `enabled` | `true` | true/false | `false` = never spawns (its biome list is emptied). |
| `biomes` | the vanilla `#tag` | one `#tag`, or a list | One `'#tag'` is used as-is. A list of biome ids and `#tags` becomes the optional tag `dwellerstructures:structure/<name>`, so a missing biome is skipped. In a list, invalid ids (not `namespace:path`) are skipped with a warning, and a list with no valid entry keeps the default. A single `#tag` that isn't valid keeps the default, with a warning. |
| `extra-biomes` | `[]` (cave biomes for underground ones) | biome ids and `#tags` | Added to the structure's biomes as **optional** entries, so a missing biome is skipped. With a single `#tag` they are added to that tag; with a list they join the list's tag. |
| `y` | `default` | -2032 to 15 | `default`, one number, or `[min, max]`. Only for jigsaw structures (marked `# y editable`). Sets a fixed Y range. Other types, and values outside the range, log a warning and are ignored. |
| `terrain-adaptation` | `beard_box` | `beard_box`, `beard_thin`, `bury`, `encapsulate`, `none` | Only with a `y`. How the rock around the structure is shaped. An unknown value falls back to `beard_box` with a warning. |

**terrain-adaptation.** `beard_box` clears a room around the structure and fills under it, so it isn't buried in solid rock; that's what makes deep trial chambers and ancient cities walkable. `beard_thin` clears less. `bury` and `encapsulate` keep the structure inside solid rock (players have to dig in). `none` places it as-is, so most of it is solid stone in a cave world.

**Ids.** `minecraft:lush_caves`, `dwellerbiomes:sandstone_caves` and `#minecraft:is_overworld` are valid. `lush_caves` without a namespace is not: in a list it is skipped, and as a single entry the default biomes are kept. Both log a warning.

### DwellerMC defaults

Mineshafts, strongholds and trial chambers get every cave biome in `extra-biomes`: `dwellerbiomes:caves`, `dwellerbiomes:sandstone_caves`, `minecraft:lush_caves`, `minecraft:dripstone_caves`, `minecraft:sulfur_caves`, `minecraft:deep_dark`.

| Structure | extra-biomes | y | Set (spacing / separation / frequency) |
|---|---|---|---|
| `ancient_city` | `[]` (vanilla tag: Deep Dark only) | `[-1950, -1450]` | `ancient_cities`: 24 / 8 / 1.0 |
| `trial_chambers` | cave biomes | `[-1990, -640]` | `trial_chambers`: 34 / 12 / 1.0 |
| `mineshaft` | cave biomes | Minecraft's own, limited to Y -2032..-17 by DwellerCore | `mineshafts`: 1 / 0 / 0.03 |
| `stronghold` | cave biomes | Minecraft's own, limited to below Y -500 by DwellerCore | `strongholds`: 64 / 32 / 1.0 (spread over the world, not rings) |

Mineshaft and stronghold heights can't be set here: Minecraft places them at a random height below sea level. DwellerCore drops the ones outside its `structures.height-limits` (see [DwellerCore config](config-core.md#structures)), so their sets are tuned up to make up for the dropped ones. Mineshafts: DwellerCore spreads their height over the whole depth, so about 97% of attempts fit inside Y -2032..-17. 0.03 gives about 3% of chunks a mineshaft, spread over every depth. This default is only written when `structures.yml` is first created; an existing server must change `sets.mineshafts.frequency` itself.

These defaults live in `DefaultConfig.java` only to write the first file. After that, `structures.yml` is the only place they are read from; change them there.

Every other structure keeps its vanilla biomes, so in a DwellerCore + DwellerBiomes world it doesn't spawn (those biomes don't exist there).

!!! warning "Roof rule"
    DwellerCore cancels any structure whose bounding box reaches **Y -16 or higher** (`StructureFilter.java`). Keep every `y` below -16, with room for the structure's full height above it. That's why `ancient_city` and `trial_chambers` default deeper than vanilla. Villages snap to the surface (the roof) and get cancelled, so underground villages don't work.

## sets.&lt;set&gt;

Structures listed in the same set share these numbers.

| Key | Valid range | Effect |
|---|---|---|
| `spacing` | 1 to 4096 | Average distance between spawn attempts, in chunks. |
| `separation` | 0 to spacing - 1 | Minimum distance between attempts, in chunks. |
| `frequency` | above 0, up to 1 | Chance each attempt spawns. |
| `distance` / `spread` / `count` | 0 to 1023 / 0 to 1023 / 1 to 4095 | Ring sets (strongholds in vanilla): rings around 0,0. Ignored when the set has `spacing`, which switches it to spread placement (`spacing` needs `separation` too). |

Out-of-range values: the whole set is skipped with a warning and stays vanilla.

Lower spacing means more of that structure: halving spacing gives about four times as many. A frequency below 1 thins them out without changing the grid. Very low spacing on big structures (ancient cities at spacing 4) makes them overlap and cut into each other, and slows chunk generation.

## custom.&lt;name&gt;

Your own structure from an `.nbt` file saved with a structure block, copied into `plugins/DwellerStructures/custom/`.

| Key | Default | Range | Effect |
|---|---|---|---|
| `file` | `<name>.nbt` | file name | File inside `custom/`. Paths outside that folder are rejected. |
| `enabled` | `true` | true/false | `false` = skipped. |
| `biomes` | `['#minecraft:is_overworld']` | ids and `#tags` | Written as optional entries (a missing biome never breaks startup). In a DwellerBiomes world, list the cave biomes. |
| `y` | **required** | -2032 to 15 | One number or `[min, max]`. Missing = skipped. |
| `spacing` / `separation` / `frequency` | 32 / 8 / 1.0 | as sets | Same rules as sets. |
| `terrain-adaptation` | `beard_box` | as above | How the rock around it is shaped. |
| `step` | `underground_structures` | a generation step | When in world generation it is placed: `raw_generation`, `lakes`, `local_modifications`, `underground_structures`, `surface_structures`, `strongholds`, `underground_ores`, `underground_decoration`, `fluid_springs`, `vegetal_decoration`, `top_layer_modification`. An unknown step falls back to the default. |

Names: lowercase letters, digits, `_`. Custom structures are single-piece jigsaw structures. The jigsaw size (1) and reach (80 blocks) are fixed in the code, because a single piece has nothing to expand. A later `step` (for example `top_layer_modification`) places it after ores and decorations, so ores and glow lichen never grow into it; an earlier one lets them.

See also: [DwellerBiomes biome names](config-biomes.md#default-biomes), [Troubleshooting](troubleshooting.md).

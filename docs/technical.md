# Technical notes

## Commands and permissions
None. No plugin declares commands or permissions (checked `paper-plugin.yml` and the code). Everything is set in config files.

## Persistent data keys
Stored on mob entities (namespace `dwellercore`, `Keys.java`):

| Key | Type | Meaning |
|---|---|---|
| `dwellercore:level` | INTEGER | Visible level 1–50. Present = leveled mob. |
| `dwellercore:hidden_level` | INTEGER | Hidden level 1–5000, drives stats. |
| `dwellercore:elite_traits` | STRING | Comma-separated trait names, e.g. `VAMPIRIC,BLINK`. Present = elite. |
| `dwellercore:elite_title` | STRING | Adjectives shown in the name, e.g. `Vampiric Blinking`. |
| `dwellercore:level_scaling` | modifier key | Key of every attribute modifier DwellerCore adds. |

Elites are re-tracked from these keys when their chunk loads, so they keep their traits across restarts.

Stored by DwellerTools (namespace `dwellertools`, `Keys.java`): see [DwellerTools config](config-tools.md#persistent-keys).

Stored by DwellerEnchants (namespace `dwellerenchants`, `Keys.java`): see [DwellerEnchants config](config-enchants.md#persistent-keys). DwellerCore reads `dwellerenchants:silenced_until` from mobs, and DwellerEnchants reads DwellerCore's level and elite keys above.

## Load order
DwellerCore, DwellerBiomes, DwellerStructures, DwellerTools and DwellerEnchants are `load: STARTUP` (DwellerEnchants after DwellerCore, which it requires). Each registers its datapack in the bootstrap phase, before worlds exist:

- DwellerCore's pack failing to register throws, so the server never generates a world at vanilla height.
- DwellerBiomes' pack failing throws for the same reason (the biomes wouldn't exist).
- DwellerStructures' pack failing only logs an error; structures stay vanilla.
- DwellerEnchants' pack failing throws, because every enchanted item would break. It is written from `config.yml` into `plugins/DwellerEnchants/datapack/` at each startup.
- DwellerTools' datapack only adds `minecraft:light` to `#minecraft:climbable`. Players climb ropes because the plugin sets their speed every tick (some clients never get the datapack tag); the tag is what lets mobs climb them.

## Datapack overrides
DwellerCore's datapack replaces three kinds of vanilla worldgen files. All of them only affect chunks generated after the change.

- `worldgen/noise_settings/overworld.json`: vanilla overworld noise settings with noise `min_y` -2032 and `height` 2048 (vanilla -64 and 384). Minecraft places mineshafts and strongholds at a random height from the noise min_y up to sea level minus 10 (Y 53). With the vanilla -64 every mineshaft landed between Y -64 and the roof and most were cancelled by the roof rule; now they spread over the whole depth.
- `worldgen/placed_feature/monster_room*.json`: dungeons, see [DwellerCore config](config-core.md#dungeons-datapack).
- `worldgen/placed_feature/spring_water.json`, `spring_lava.json`, `lake_lava_underground.json`: cave water and lava, see [DwellerCore config](config-core.md#cave-water-and-lava-datapack).

## Performance notes (from the code)
- Cave density is sampled on a 4×8×4 grid and interpolated, like vanilla, over a ~2000-block column.
- `caves.biome-boost` adds a biome lookup per grid point; it's skipped while the map is empty.
- The spawn search scans at most 8 columns (one chunk load each) before carving a room.
- Name tags update every 10 ticks for mobs within 24 blocks of a player. Elite abilities and boss bars tick every 20 ticks.
- DwellerCore downloads the resource pack's SHA-1 once at startup, off the main thread. See [Resource pack](resource-pack.md).
- DwellerTools' Hammer breaks the extra blocks of its square with `Player#breakBlock` from a MONITOR `BlockBreakEvent` handler. A per-player recursion guard stops those breaks from starting another square. The face comes from the player's last `BlockDamageEvent`.
- The Paxel's mining rules are a `tool` data component set at craft time. The Drill's Efficiency is an unsafe enchantment re-applied to the result in `PrepareAnvilEvent` and `PrepareGrindstoneEvent`.
- DwellerTools checks ropes every `rope.check-ticks` (5) ticks and only loops over ropes that exist.

See also: [DwellerCore config](config-core.md), [Troubleshooting](troubleshooting.md).

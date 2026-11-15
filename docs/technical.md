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

## Load order
All three plugins are `load: STARTUP`. Each registers its datapack in the bootstrap phase, before worlds exist:

- DwellerCore's pack failing to register throws, so the server never generates a world at vanilla height.
- DwellerBiomes' pack failing throws for the same reason (the biomes wouldn't exist).
- DwellerStructures' pack failing only logs an error; structures stay vanilla.

## Performance notes (from the code)
- Cave density is sampled on a 4×8×4 grid and interpolated, like vanilla, over a ~2000-block column.
- `caves.biome-boost` adds a biome lookup per grid point; it's skipped while the map is empty.
- The spawn search scans at most 8 columns (one chunk load each) before carving a room.
- Name tags update every 10 ticks for mobs within 24 blocks of a player. Elite abilities and boss bars tick every 20 ticks.

See also: [DwellerCore config](config-core.md), [Troubleshooting](troubleshooting.md).

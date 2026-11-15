# Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `/tp @s ~ -2000 ~` says out of the world | World was made without the `dwellercore` datapack, or is an old world | Check `/datapack list enabled`. Generate a fresh world ([Install](install.md)). |
| Server crashes with "Feature order cycle" | Two biomes list shared features in a different order | Use the same order in every biome; reuse `*underground` ([rule](config-biomes.md#feature-order-rule)). |
| Log: `Config biomes.<name>.biome: biome ... is not loaded` (biome not placed) | A biome was added and the server was `/reload`ed, or the name is wrong | Full restart. Check spelling in `config.yml` ([DwellerBiomes](config-biomes.md)). |
| Log: `[DwellerBiomes] Config ...: unknown block` | Typo in a block name; that block is skipped | Fix the block name ([DwellerBiomes](config-biomes.md)). |
| Log: `Its features were NOT removed, so its vanilla ores generate in new chunks` | A `biome: minecraft:...` id doesn't exist in the server jar | Fix the id and restart before more chunks generate ([DwellerBiomes](config-biomes.md)). |
| A structure stopped spawning after editing `structures.yml` | Every biome in its `biomes:` list is missing (they are optional, so missing ones are skipped) | Check the ids; list the cave biomes for an underground world ([DwellerStructures](config-structures.md)). |
| Log: `Could not build the structure datapack; structures stay vanilla` | `structures.yml` isn't valid YAML | Fix the YAML and restart ([DwellerStructures](config-structures.md)). |
| Log: `generator doesn't use the biome provider: structures won't see custom biomes` | World isn't using DwellerCore's generator | Set `generator: DwellerCore` in `bukkit.yml` for that world. |
| A structure never appears | Its biomes don't exist in the cave world, or it reaches Y -16 and gets cancelled | Add cave biomes to `extra-biomes`; keep `y` below -16. |
| Log: `Config ores.types.<name>: not an ore, or veins/size are not numbers from 0 to 1000. Ore skipped` | `veins` or `size` isn't a number in range, or the ore name doesn't match a block | Fix `ores.types` ([DwellerCore](config-core.md#ores-new-chunks)). |
| Log: `[DwellerCore] Config <key>: got ..., expected ... Using the default` | A DwellerCore value has the wrong type or is out of range; the server runs on the default | Fix the key; the ranges are in the [DwellerCore config](config-core.md) tables. Restart. |
| Surface mobs have no level, or no suit pieces drop | World name differs from `mobs.fixed-level-worlds`, or DwellerCore is missing | See [DwellerSurface problems](config-surface.md#common-problems). |
| A config change did nothing | Old chunks keep their old generation; mobs keep spawn-time stats | Explore new chunks or start a fresh world. |

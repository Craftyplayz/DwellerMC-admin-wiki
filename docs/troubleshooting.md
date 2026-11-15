# Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `/tp @s ~ -2000 ~` says out of the world | World was made without the `dwellercore` datapack, or is an old world | Check `/datapack list enabled`. Generate a fresh world ([Install](install.md)). |
| Server crashes with "Feature order cycle" | Two biomes list shared features in a different order | Use the same order in every biome; reuse `*underground` ([rule](config-biomes.md#feature-order-rule)). |
| Startup error `Biome ... is not loaded` | A biome was added and the server was `/reload`ed, or the name is wrong | Full restart. Check spelling in `config.yml`. |
| Startup error `Unknown block in 'replace'` | Typo in `blocks.replace` | Fix the block name ([DwellerBiomes](config-biomes.md)). |
| Server won't start after editing `structures.yml` | A biome id in a `biomes:` list doesn't exist | Fix or remove it. Prefer `extra-biomes` (optional entries). |
| Log: `Could not build the structure datapack; structures stay vanilla` | `structures.yml` isn't valid YAML | Fix the YAML and restart ([DwellerStructures](config-structures.md)). |
| Log: `generator doesn't use the biome provider: structures won't see custom biomes` | World isn't using DwellerCore's generator | Set `generator: DwellerCore` in `bukkit.yml` for that world. |
| A structure never appears | Its biomes don't exist in the cave world, or it reaches Y -16 and gets cancelled | Add cave biomes to `extra-biomes`; keep `y` below -16. |
| Log: `Skipping invalid ore config` | `veins` isn't a single number, or the ore name doesn't match a block | Fix `ores.types` ([DwellerCore](config-core.md#ores-new-chunks)). |
| A config change did nothing | Old chunks keep their old generation; mobs keep spawn-time stats | Explore new chunks or start a fresh world. |

# DwellerMC Admin Wiki

Admin-only reference for the DwellerMC server.

Requires **Paper 26.3** and **Java 25**. All three plugins load at `STARTUP` (`paper-plugin.yml`).

## The three plugins

| Plugin | Does | Datapack it registers |
|---|---|---|
| **DwellerCore** | World generator (caves, rock, ores, bedrock floor), player spawn, depth HUD, mob levels, gear, perks, rewards, spawn mix, elites | `dwellercore`: built into the jar; sets the overworld dimension to start at Y -2032 |
| **DwellerBiomes** | Bedrock roof, cave biome patches, rock restyling and decorations | `dwellerbiomes`: generated at startup from `config.yml` into `plugins/DwellerBiomes/datapack/` |
| **DwellerStructures** | Where structures spawn and how common they are; custom `.nbt` structures | `dwellerstructures`: generated at startup from `structures.yml` into `plugins/DwellerStructures/generated-datapack/` |

## How they connect

1. **DwellerCore** is the world generator (`bukkit.yml` → `generator: DwellerCore`). It generates solid rock from Y -2032 to Y 0, carves caves below Y -17, places ores and the bedrock floor. It leaves the roof band (Y -16 to 0) solid.
2. **DwellerBiomes** registers a `BiomeProvider` as a Bukkit service. DwellerCore's generator looks it up in `getDefaultBiomeProvider`, so structures, features and mob spawns see the DwellerBiomes biomes. There is no compile-time link: without DwellerBiomes, DwellerCore falls back to vanilla biomes.
3. DwellerBiomes also attaches a **populator** to each world in its `worlds:` list. It places the bedrock roof and restyles rock inside biome patches, using the same per-cell decision as the provider, so blocks always match F3.
4. **DwellerStructures** rewrites vanilla structure JSON. Structures check the biome the provider reports, so underground structures only spawn in DwellerBiomes biomes listed in their `extra-biomes`.
5. DwellerCore cancels any structure whose bounding box reaches Y -16 or higher (`StructureFilter`).

All DwellerCore gameplay (mobs, spawn, HUD) only runs in worlds whose generator is DwellerCore's.

## Repos (GitHub: Craftyplayz)
Plugins (private): `DwellerCore`, `DwellerBiomes`, `DwellerStructures`. Build each with `./gradlew build`. Wikis (public): this one (unlisted) and the [player wiki](https://craftyplayz.github.io/DwellerMC-wiki/).

## Other plugins
Third-party and helper plugins on the server each have a page under **Plugins**: permissions and safety ([LuckPerms](plugins/luckperms.md), [CoreProtect](plugins/coreprotect.md), [WorldGuard](plugins/worldguard.md)), [EssentialsX](plugins/essentials.md), mining ([Veinminer](plugins/veinminer.md), [CraftyTools](plugins/craftytools.md)), gameplay and community plugins.

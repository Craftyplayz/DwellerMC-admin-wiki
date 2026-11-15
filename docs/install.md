# Install & fresh world

!!! warning "Fresh world only"
    DwellerCore changes the world height and generator. An existing world keeps its old chunks. Always start these plugins on a newly generated world.

## Steps

1. Build each plugin: `./gradlew build`. Jars land in `build/libs/`:
    - `DwellerCore-<version>.jar`
    - `DwellerBiomes-<version>.jar`
    - `DwellerStructures-<version>.jar`
2. Copy the jars into the server's `plugins/` folder.
3. In `bukkit.yml`, set the generator (use your level name if it isn't `world`):
    ```yaml
    worlds:
      world:
        generator: DwellerCore
    ```
4. Make sure the level name is in DwellerBiomes' `worlds:` list (default `[world]`).
5. Delete or rename the old `world` folder.
6. Start the server.

## Optional: the surface

[DwellerSurface](config-surface.md) is a fourth plugin. Build it with `./gradlew build`, copy the jar into `plugins/`, restart, then build and mark the two portals as described in [first-time setup](config-surface.md#first-time-setup). It needs no `bukkit.yml` entry and works with DwellerCore installed or not.

## Restart, never /reload

All three plugins build or register datapacks during bootstrap, before worlds load. Config changes need a **full restart**. World-generation changes only affect **chunks generated after the restart**.

## Check it worked

| Command | Expected |
|---|---|
| `/datapack list enabled` | Shows `dwellercore`, `dwellerbiomes`, `dwellerstructures` |
| `/tp @s ~ -2000 ~` | Works (no "out of the world" error) |
| `/locate biome dwellerbiomes:sandstone_caves` | Finds a location |
| `/locate structure minecraft:ancient_city` | Finds a location |

Console lines to look for:

- DwellerBiomes: `Roof and cave biomes enabled in world 'world'`. If it adds `(generator doesn't use the biome provider: structures won't see custom biomes)`, the world isn't using DwellerCore's generator.
- DwellerStructures: `Structure datapack built: N vanilla overrides, N custom structures`.

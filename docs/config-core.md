# DwellerCore config

File: `plugins/DwellerCore/config.yml` (default: `DwellerCore/src/main/resources/config.yml`).

**When changes apply:** every key needs a server restart (there is no reload command). Keys marked **new chunks** only change chunks generated after the restart. Mob keys only change mobs that spawn after the restart; existing mobs keep the stats they spawned with.

**Bad values never stop the server.** Every key is checked when the plugin starts. A wrong type (text where a number belongs) or a number outside the range in the tables below logs a warning and the default is used:

```
[DwellerCore] Config mobs.max-armor: got '45', expected a number from 0 to 30. Using the default: 12
```

Bad rows in a list (a drop table row, an unknown mob id) are skipped with a warning and the rest of the list is kept. A key missing from your file uses the default from the jar, so an old `config.yml` keeps working after an update. To see every new key, rename your file and restart: the plugin writes a fresh one.

**Power.** Mob scaling uses `p = (hidden / 5000) ^ curve-exponent`, where `hidden = (level - 1) * 100 + random 1..100` (`MobLevels.java`). `p` is about 0 at level 1 and about 1 at level 50. Every `max-*` value is the amount at `p = 1`; a level 25 mob (p about 0.39) gets about 39% of it.

## Fixed values (not settings)

These stay in the code on purpose; each has a comment there saying why.

| Value | Where | Why it is fixed |
|---|---|---|
| World bottom Y -2032, top Y 15 | `Depth.java`, `datapack/.../dimension_type/overworld.json` | Set by the built-in datapack, which the server reads before plugins load. -2032 is the lowest Minecraft allows. |
| Roof band Y -16 to 0, air above Y 0, caves only below Y -17 | `DwellerGenerator.java` | [DwellerBiomes](config-biomes.md) builds the roof in exactly this band and every generated world already has its ceiling here. |
| Hidden level 1 to 5000 (100 per level) | `MobLevels.java` | Stored on every leveled mob (`dwellercore:hidden_level`); a new scale would change the power of mobs that already exist. |
| Cave density grid 4 x 8 x 4 blocks | `DwellerGenerator.java` | A speed/accuracy trade-off of the generator; changing it shifts every cave wall. |
| Elite ability tick once a second | `EliteManager.java` | Trait timers (`every-seconds`) count these ticks. |
| Persistent data keys | `Keys.java` | Stored on mobs; see [Technical notes](technical.md#persistent-data-keys). |

## world

| Key | Default | Range | Effect |
|---|---|---|---|
| `world.cave-top-y` | `0` | -2031 to 15 | Y where depth is 0 and area level is 1. Used by the HUD, mob levels, spawn mix and elite chance (`Depth.java`). |

Area level = `1 + round(depthFraction x (max-level - 1))`, with `depthFraction` 0 at `cave-top-y` and 1 at Y -2032. Lowering `cave-top-y` to -500 makes everything between -500 and the roof count as level 1, and squeezes all 50 levels into the deeper part. It does **not** move the roof or the caves. The player wiki tables assume 0.

## caves (new chunks)

Three noise fields carve the caves: rooms, voids and tunnels. A block is air where any field says so (`CaveNoise.java`).

| Key | Default | Range | Effect |
|---|---|---|---|
| `caves.biome-boost` | `{}` | -1 to 1 per biome | Biome id to extra density for rooms and voids (not tunnels). `0.1` makes rooms about twice as big; negative values shrink them. |
| `caves.ceiling-fade` | `16` | 1 to 500 | Blocks under the roof over which caves fade to solid rock. |
| `caves.rooms.threshold` | `0.27` | -1 to 1 | Noise above this is air. Lower = more and bigger rooms. |
| `caves.rooms.octaves` | `3` | 1 to 8 | Detail layers. More = rougher walls, slightly slower generation. |
| `caves.rooms.horizontal-scale` | `96` | 1 to 10000 | Room feature width in blocks. |
| `caves.rooms.vertical-scale` | `64` | 1 to 10000 | Room feature height in blocks. |
| `caves.voids.threshold` | `0.45` | -1 to 1 | Same as rooms, for the huge voids. |
| `caves.voids.octaves` | `2` | 1 to 8 | |
| `caves.voids.horizontal-scale` | `300` | 1 to 10000 | |
| `caves.voids.vertical-scale` | `200` | 1 to 10000 | |
| `caves.tunnels.radius` | `0.07` | 0 to 1 | Tunnel width. Tunnels follow lines where two noise fields both cross zero. |
| `caves.tunnels.octaves` | `2` | 1 to 8 | Used by both tunnel fields. |
| `caves.tunnels.horizontal-scale` | `48` | 1 to 10000 | |
| `caves.tunnels.vertical-scale` | `48` | 1 to 10000 | |
| `caves.octave-frequency` | `2.0` | 0.1 to 10 | Each octave has this many times the frequency of the one before. Shared by all fields. |
| `caves.octave-amplitude` | `0.5` | 0.01 to 1 | Each octave has this fraction of the strength of the one before. |

**biome-boost.** Uses the biome from DwellerBiomes' provider at each sample point. With an empty map the generator skips biome lookups entirely, so leave it empty unless you use it. Example: `{ "minecraft:lush_caves": 0.12, "minecraft:deep_dark": -0.05 }`.

**Thresholds.** Small changes are big: rooms at `0.2` turn most of the world into open space, at `0.35` most rooms disappear. Voids at `0.4` are roughly twice as common as at `0.45`. Tunnel radius above `0.12` turns tunnels into wide corridors that dominate the map. Values below 0 make that field air almost everywhere, which leaves almost no rock (and almost no ore, since ores only replace rock).

**Scales.** Doubling a scale doubles the size of the shapes in that direction. Vertical scales far above horizontal give tall shafts; far below give flat, pancake-like caves.

**What breaks.** Nothing crashes, but the new chunks no longer line up with the old ones: a seam of mismatched cave walls appears at the border. Change cave keys only before the world is pre-generated, or accept the seams.

## rock (new chunks)

| Key | Default | Range | Effect |
|---|---|---|---|
| `rock.deepslate-y` | `-300` | -2031 to 0 | Stone at and above this Y, deepslate below. |
| `rock.deepslate-blend` | `8` | 0 to 64 | Height of the mixed band just below `deepslate-y`. Inside it the chance of deepslate rises linearly from top to bottom. `0` = a sharp line. |
| `rock.bedrock-floor-layers` | `4` | 0 to 16 | Rough bedrock layers above the solid floor at Y -2032. Layer `n` is bedrock with chance `(layers + 1 - n) / (layers + 1)`: with 4, 80%, 60%, 40%, 20%. `0` = one flat layer. |
| `rock.lava-sea-height` | `6` | 0 to 64 | Cave air from Y -2031 up to this many blocks above Y -2032 is generated as lava source blocks, so every cave that reaches the bottom ends in a flat lava sea. With 6 the sea surface is Y -2026, two blocks above the highest bedrock layer. `0` = off, bare bedrock floors. |

Ores pick their stone or deepslate variant from the rock they replace, so `deepslate-y` also moves where deepslate ores start. Mining speed follows the rock: deepslate is twice as slow to mine. The player wiki shows -300 and the 8-block band on [World layout](https://craftyplayz.github.io/DwellerMC-wiki/world/layout/) and in the ore calculator; update `DW.deepslateY` in the wiki if you change it.

**lava-sea-height.** Keep it above `bedrock-floor-layers`, or the tops of the bedrock bumps stick out of the lava. The lava is placed as still source blocks during generation, before ores, so ores touching the sea count as buried and follow `ores.buried-chance`. A big value (like 64) floods the lowest caves and hides a lot of ore under lava. Chunks generated before the change keep their bare floor, so a seam can show where old and new chunks meet.

## structures

| Key | Default | Used in | Effect |
|---|---|---|---|
| `structures.height-limits` | stronghold `[-2032, -500]`, mineshaft and mineshaft_mesa `[-2032, -17]` | `StructureFilter.java` | Structure id to `[min, max]`. The whole bounding box must fit inside, or the structure is cancelled when it generates. |

Any structure reaching Y -16 or higher is always cancelled, with or without a limit. Minecraft picks mineshaft and stronghold heights uniformly from the noise min_y (Y -2032, see the [noise settings override](technical.md#datapack-overrides)) up to Y 53, so the default mineshaft limit drops only the top few percent and the stronghold limit also drops its upper quarter. Use limits for structures whose height Minecraft picks itself (strongholds, mineshafts); jigsaw structures get a fixed `y` in [DwellerStructures](config-structures.md) instead. Cancelled attempts are simply missing, so raise the set's frequency or lower its spacing to compensate. An entry that isn't `[min, max]` with min at or below max is skipped with a warning.

## Dungeons (datapack)

Not a config key: `src/main/resources/datapack/data/minecraft/worldgen/placed_feature/monster_room*.json`, written by `tools/dungeons.py`.

- `monster_room` (vanilla Y 0 and up, which would be the roof): count 0.
- `monster_room_deep`: 89 attempts per chunk, spread over 50-block bands with a weighted height list. Density: none above Y -250, x0.6 at -250, x1 at -1000, x2 at -1500, x2 down to -2026, linear between. x1 = vanilla's average of 14 attempts per chunk over 378 blocks of height.
- Applies to every biome, because all cave biomes list these two features.
- To change it: edit `POINTS` in `tools/dungeons.py`, run `py tools/dungeons.py`, rebuild, restart. New chunks only.

Datapack files are read by the server before plugins load, which is why this one can't be a config key.

## Cave water and lava (datapack)

Not a config key: `src/main/resources/datapack/data/minecraft/worldgen/placed_feature/{spring_water,spring_lava,lake_lava_underground}.json`, written by `tools/fluids.py`.

- `spring_water`: 196 attempts per chunk, uniform over Y -2025 to -17 (vanilla's density per block of height).
- `spring_lava`: 107 attempts per chunk, weighted 3 times more common at the bottom than the top, in 100-block bands.
- `lake_lava_underground`: 48 attempts per chunk. An attempt must start in cave air and drop up to 32 blocks to a cave floor. Half the lakes are water, half lava. Vanilla only places underground lava lakes above Y 0, which is this world's roof, so there were none before.
- Applies to every biome that lists these vanilla features: all DwellerBiomes cave biomes and the vanilla cave biomes (the Deep Dark has none).
- To change it: edit the numbers at the top of `tools/fluids.py`, run `py tools/fluids.py`, rebuild, restart. New chunks only.

## spawn

| Key | Default | Range | Effect |
|---|---|---|---|
| `spawn.random-cave` | `false` | true/false | `true`: first join and bed-less respawns go to a random cave. `false`: left to the server spawn, which is [EssentialsSpawn](plugins/essentials.md) `/setspawn` on this server. |
| `spawn.min-y` | `-600` | -2031 to 13 | Lowest Y scanned for a spawn spot. |
| `spawn.max-y` | `-300` | -2031 to 13 | Highest Y scanned. Must not be below `min-y`; if it is, both fall back to the defaults. |
| `spawn.radius` | `256` | 0 to 100000 | Random X/Z offset from the world spawn. |
| `spawn.search-attempts` | `8` | 1 to 64 | Random columns tried. Each one loads a chunk, so high values can lag a join. |
| `spawn.unsafe-floors` | `[magma_block]` | block ids | Blocks a player is never placed on. Unknown ids are skipped with a warning. |
| `spawn.pocket-floor` | `deepslate` | solid block id | Floor of the room carved when no spot was found. |
| `spawn.pocket-radius` | `1` | 0 to 4 | Carved room size: `0` = 1x1, `1` = 3x3, `2` = 5x5. |
| `spawn.pocket-height` | `3` | 2 to 8 | Air blocks above the pocket floor. |

**Beds always win**, whatever `random-cave` is set to: DwellerCore notes a bed or anchor respawn location at priority LOWEST and restores it at HIGHEST, so EssentialsSpawn or another plugin can't move it.

With `random-cave: true`, each attempt picks a random column within `radius` and scans it from `max-y` down to `min-y` for a solid floor (not in `unsafe-floors`) with 2 air blocks above. If every attempt fails, a pocket is carved at the middle Y of the range in the last column tried. A narrow range (for example `-310` to `-300`) fails more often and carves more pockets. Adding lava-adjacent blocks like `campfire` to `unsafe-floors` is safe; adding `stone` or `deepslate` makes almost every search fail.

## hud

| Key | Default | Range | Effect |
|---|---|---|---|
| `hud.enabled` | `true` | true/false | Action bar in DwellerCore worlds. |
| `hud.interval-ticks` | `20` | 1 to 1200 | Update interval (20 = once a second). Lower is smoother but sends more packets. |
| `hud.format` | `<gray>Depth <depth> · Area Lv <level>` | MiniMessage | `<depth>` is formatted with thousands separators (`1,240`), `<level>` is the area level. |

Example: `"<gold>⛏ <white><depth></white> deep <dark_gray>|</dark_gray> Lv <level>"`. A malformed tag shows as plain text; it never errors. Other plugins that also use the action bar (for example a scoreboard plugin) will flicker against it; set `enabled: false` if one of them should win.

## resource-pack

Sends the server resource pack on join. Details, repo layout and the release flow: [Resource pack](resource-pack.md). Keep `resource-pack` in `server.properties` empty.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `resource-pack.enabled` | `true` | boolean | `false` sends no pack and never kicks. Custom items from [DwellerTools](config-tools.md) then show their base items unless you deliver the pack another way. |
| `resource-pack.url` | `https://github.com/Craftyplayz/DwellerMC-resourcepack/releases/download/pack/DwellerMC.zip` | link | Download link of the pack zip. An empty link sends nothing. The pack id is made from this link, so a new link replaces the old pack on the client instead of stacking. |
| `resource-pack.sha1-url` | `.../pack/DwellerMC.zip.sha1` | link | Text file holding the zip's SHA-1, downloaded once at startup (10 s connect, 15 s read timeout). A new pack release then only needs a restart. If it cannot be read (not a 40-character hex hash, HTTP error, no connection), the log warns and the pack is sent without a hash, so players may keep an old copy. Empty = no hash. |
| `resource-pack.required` | `true` | boolean | `true`: declining the pack, a failed download or an invalid link kicks the player with `kick-message`. `false`: the pack is offered and can be skipped. |
| `resource-pack.prompt` | `<gold>DwellerMC needs its resource pack for custom items.` | MiniMessage | Text on the pack screen. |
| `resource-pack.kick-message` | red line plus a grey hint (set Server Resource Packs to Enabled in the server list) | MiniMessage | Shown to kicked players. `
` is a line break. |

If the pack host is down while `required` is `true`, nobody can join. Set `required: false` until it is back.

## ores (new chunks)

| Key | Default | Range | Effect |
|---|---|---|---|
| `ores.buried-chance` | `0.1` | 0 to 1 | Ore blocks with no face touching air are placed at this rate. Faces on the chunk edge don't count as touching air. |
| `ores.layer-height` | `64` | 8 to 512 | Height of one ore layer, counted from Y -2032. Vein counts are per 16 x 16 x layer-height, and the depth multiplier is read once per layer at its middle. |
| `ores.depth-multiplier` | `[[-400, 1], [-1000, 2], [-2032, 3]]` | multiplier 0 to 100 | `[y, multiplier]` points, blended linearly, flat above the top point and below the bottom one. |
| `ores.types.<name>.veins` | see below | 0 to 1000 | Veins per layer, times the multiplier. Fractions are rounded randomly. |
| `ores.types.<name>.size` | see below | 0 to 1000 | Blocks per vein (random walk). Default 4 if missing. |

Default types:

| Ore | veins | size |
|---|---|---|
| coal | 7 | 12 |
| copper | 8 | 10 |
| iron | 8 | 8 |
| gold | 3 | 8 |
| redstone | 4 | 7 |
| lapis | 3 | 6 |
| diamond | 2 | 6 |
| emerald | 0.2 | 3 |

`<name>` maps to `<name>_ore` in stone and `deepslate_<name>_ore` in deepslate. If `<name>_ore` doesn't exist, `<name>` itself is tried (e.g. `ancient_debris`), and the same block is used in deepslate. Ores only replace `STONE` and `DEEPSLATE`. An entry that isn't a block or has a bad `veins`/`size` is skipped with a warning; the other ores still generate.

**layer-height** changes the amount of ore too: veins are per layer, so `32` doubles all ore and `128` halves it. Change `veins` instead if you only want more or less ore. Depth-multiplier points that aren't `[y, number]` are skipped with a warning; if none is valid, the default curve is used.

**buried-chance** at `1` makes strip mining as good as caving; at `0` ore only shows in cave walls. The [Veinminer](plugins/veinminer.md) page and the player wiki ore pages assume `0.1`.

## mobs

Applies to hostile mobs (`Enemy`) in DwellerCore worlds and in worlds listed in `mobs.fixed-level-worlds`, except types in `no-scale`. Citizens NPCs are always skipped.

### Levels and stats

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.max-level` | `50` | 1 to 100 | Area level at the world bottom. |
| `mobs.level-spread` | `2` | 0 to 50 | Mob level = area level plus or minus this (random), clamped to 1..max-level. |
| `mobs.curve-exponent` | `1.32` | 0.1 to 10 | Exponent in the power formula. |
| `mobs.max-health-multiplier` | `6.0` | 1 to 1000 | Health x `1 + (6 - 1) x p`. |
| `mobs.max-damage-multiplier` | `3.0` | 1 to 1000 | Attack damage x `1 + (3 - 1) x p`. |
| `mobs.max-speed-bonus` | `0.4` | 0 to 10 | Speed x `1 + 0.4 x p`. |
| `mobs.max-armor` | `12` | 0 to 30 | `+12 x p` armor points. |
| `mobs.max-toughness` | `6` | 0 to 20 | `+6 x p` armor toughness. |
| `mobs.max-knockback-resistance` | `0.5` | 0 to 1 | `+0.5 x p` knockback resistance (1 = immune). |
| `mobs.follow-range-min` / `-max` | `16` / `48` | 0 to 2048 | Follow range = max(vanilla, `16 + 32 x p`). |
| `mobs.max-zombie-reinforcements` | `0.5` | 0 to 1 | Zombie reinforcement chance `+0.5 x p`. The bonus is off while the zombie is silenced (vanilla's own chance stays). |
| `mobs.piglins-immune-to-zombification` | `true` | true/false | Piglins and brutes never turn into zombified piglins. `false` = vanilla (they convert after 15 s in the overworld). |
| `mobs.silence-respected` | `true` | true/false | `true`: a mob hit by [DwellerEnchants](config-enchants.md)' Silence loses every DwellerCore ability for the Silence duration: elite traits (active, passive and Explosive death), status hits, archer aim and double shot, strong witch potions, extra slime splits, spider leaps, enderman teleports and the zombie reinforcement bonus. Stats, gear, name tag, elite particles and boss bar stay. `false` = Silence does nothing. No effect without DwellerEnchants. |
| `mobs.level-spawner-mobs` | `false` | true/false | `false`: mobs from spawners (`SpawnReason.SPAWNER`: dungeons and placed spawners) stay vanilla and never become elites. Trial spawners are always leveled. |
| `mobs.fixed-level-worlds` | `world_surface: 50` | level 1 to max-level | World name to a fixed area level, for worlds DwellerCore did not generate. |
| `mobs.no-scale` | `[WARDEN, WITHER, ENDER_DRAGON, ELDER_GUARDIAN]` | mob ids | Never leveled, never swapped by spawn mix. Case doesn't matter. |

**max-level** above 50 pushes power past 1, because the power curve divides by 5000 (50 levels). A level 60 mob would get about 1.26x every `max-*` value. The [player wiki](https://craftyplayz.github.io/DwellerMC-wiki/mobs/levels/) level tables are built for 50.

**curve-exponent.** `1` = straight line (level 25 has 50% power). `1.32` = level 25 has about 40%. `2` = level 25 has 25%, so the top third of the world is much easier and the bottom much harder. Values below 1 make shallow mobs strong early.

**Health and damage.** Health 6x and damage 3x at level 50 are already tough for diamond gear; above 10x damage, level 50 mobs one-shot players in netherite. Armor above 20 with toughness 8+ makes mobs nearly immune to unenchanted weapons.

**fixed-level-worlds.** For the [DwellerSurface](config-surface.md) world. Hostile mobs there get leveled with area level = the number (spread still applies), no depth involved, and show the name tag. Elite chance there uses `(level - 1) / (max-level - 1)` as the depth fraction, so 50 gives the bottom-of-world chance from `elites.chance`. The name must match the world exactly; a wrong name means no levels. Spawn mix, the HUD and the generator still only run in DwellerCore worlds.

**level-spawner-mobs: true** makes dungeon spawners pump out level 40+ mobs at the bottom of the world, and those can roll elite, which turns a dungeon into a boss farm.

### Creepers

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.creeper.base-explosion-radius` | `3` | 0 to 50 | Explosion radius at level 0 (vanilla 3). |
| `mobs.creeper.explosion-radius-every-levels` | `12` | 0 to 1000 | Radius +1 for every N levels: level 12 = 4, level 48 = 7. `0` = never grows. |
| `mobs.creeper.base-fuse-ticks` | `30` | 1 to 600 | Fuse at power 0 (vanilla 30 = 1.5 s). |
| `mobs.creeper.max-fuse-reduction-ticks` | `15` | 0 to base - 1 | Fuse = `30 - 15 x p` ticks, so level 50 creepers blow up in about 0.75 s. |

Radius 7 already blows big holes in cave walls; radius 10+ breaks through into neighbouring caves and can expose dungeons. A reduction equal to or above the base fuse is rejected (the creeper would explode instantly).

### Name tags

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.name-tag-distance` | `12` | 0 to 128 | Name tag shows when a player is within this many blocks. |
| `mobs.display.update-ticks` | `10` | 1 to 200 | How often names (and their health) update. |
| `mobs.display.scan-radius` | `24` | 1 to 128 | Only mobs within this many blocks of a player get updates and abilities. |
| `mobs.display.name-format` | `Lv <level> <title><mob> ❤ <health>/<max_health>` | MiniMessage | `<title>` is the elite adjectives plus a space, or nothing. `<mob>` is the mob name in the player's language. |
| `mobs.display.name-colors.up-to-level` | `{ 10: green, 20: yellow, 35: red, 45: light_purple }` | color names or `#rrggbb` | Level up to and including N gets that color. |
| `mobs.display.name-colors.higher` | `dark_red` | color | Levels above the last entry. |
| `mobs.display.name-colors.elite` | `gold` | color | Elites, at any level. |

The color applies to the whole tag unless `name-format` sets its own colors. `name-tag-distance` above `scan-radius` has no effect beyond the scan radius, because mobs outside it are not updated. `scan-radius` above 48 costs noticeable CPU on a busy server: each update searches a cube of that size around every player. The player wiki [mob levels](https://craftyplayz.github.io/DwellerMC-wiki/mobs/levels/) page lists the colors.

### Status hits

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.status-hit-max-chance` | `0.35` | 0 to 1 | Chance per hit on a player = `0.35 x p` to add one random effect. |
| `mobs.status-hit-effects` | `[slowness, weakness, hunger, poison, mining_fatigue]` | effect ids | The effects picked from, as in `/effect`. Unknown ids are skipped with a warning; an empty list turns status hits off. |
| `mobs.status-hit-min-ticks` / `max-ticks` | `60` / `120` | 1 to 72000; max not below min | Effect length, random between these (3 to 6 s). A `max-ticks` below `min-ticks` falls back to 120. |
| `mobs.status-hit-level-2-from` | `35` | 1 to 1001 | Mobs of this level and up give level II. |

Adding `wither` or `blindness` to the list is a big difficulty jump in dark caves. Ranged hits count too: the effect comes from the shooter. Status hits are skipped while the mob is silenced ([`mobs.silence-respected`](#levels-and-stats)).

### Abilities

Spider leaps, enderman teleports, archer aim and double shots are skipped while the mob is silenced ([`mobs.silence-respected`](#levels-and-stats)).

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.abilities.interval-ticks` | `20` | 1 to 1200 | How often spider leaps and enderman teleports are checked, rounded to whole `update-ticks`. |
| `mobs.abilities.spider-leap.min-level` | `10` | 1 to 1000 | Spiders of this level and up can leap at their target. |
| `mobs.abilities.spider-leap.chance` | `0.25` | 0 to 1 | Chance per check (while on the ground). |
| `mobs.abilities.spider-leap.min-distance` / `max-distance` | `3` / `8` | 0 to 64 | Only leaps from this distance range, in blocks. |
| `mobs.abilities.spider-leap.base-speed` / `max-extra-speed` | `0.6` / `0.8` | 0 to 5 | Horizontal speed = `0.6 + 0.8 x p`. |
| `mobs.abilities.spider-leap.lift` | `0.4` | 0 to 5 | Upward speed of the leap. |
| `mobs.abilities.enderman-teleport.max-chance` | `0.3` | 0 to 1 | Chance per check = `0.3 x p` that an enderman teleports next to its target. |
| `mobs.abilities.enderman-teleport.attempts` | `8` | 0 to 64 | Spots tried; `0` turns it off. |
| `mobs.abilities.enderman-teleport.radius` | `3` | 0.5 to 32 | Spots are within this many blocks of the target... |
| `mobs.abilities.enderman-teleport.min-distance` | `1.5` | 0 to 32 | ...but not closer than this. |
| `mobs.abilities.archers.aim-correction` | `1.0` | 0 to 1 | Skeletons and pillagers blend their shot toward perfect aim by `p x this`. `0` = vanilla aim. |
| `mobs.abilities.archers.aim-height` | `0.7` | 0 to 1 | Aim point as a fraction of the target's height (0.7 = chest). |
| `mobs.abilities.archers.arrow-drop-compensation` | `0.015` | 0 to 0.2 | Aim this much higher per block of distance. |
| `mobs.abilities.archers.double-shot-max-chance` | `0.5` | 0 to 1 | Chance `0.5 x p` to fire a second arrow, which can't be picked up. |
| `mobs.abilities.archers.double-shot-delay-ticks` | `8` | 1 to 100 | Delay before the second arrow. |
| `mobs.abilities.witch-strong-potions-from-level` | `25` | 1 to 1001 | Witches of this level and up throw the strong (level II) version of their potions. Above `max-level` = never. |
| `mobs.abilities.slime-extra-split-every-levels` | `15` | 0 to 1000 | Slimes and magma cubes split into 1 extra small slime for every N levels (level 45 = 3 extra). `0` = vanilla. |

A spider `lift` above 1 throws spiders over players' heads; `base-speed` above 2 makes them overshoot. `arrow-drop-compensation` above 0.05 makes arrows fly over the target at close range. Low `slime-extra-split-every-levels` (like 3) at the bottom of the world creates dozens of slimes from one kill, which hits server performance.

### Gear

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.gear.drop-chance` | `0.0` | 0 to 1 | Drop chance for every worn and held item, including vanilla gear. `0` = gear never drops. |
| `mobs.gear.mobs` | `[zombies, skeletons, piglins, vindicator, pillager]` | groups or mob ids | Who gets armor and weapons. Groups: `zombies` (zombie, husk, drowned, zombie villager, zombified piglin), `skeletons` (every skeleton kind), `piglins` (piglin and brute). |
| `mobs.gear.tiers` | see below | rows | Armor material, weapon material and armor chance by level. |
| `mobs.gear.enchant-chance-multiplier` | `1.5` | 0 to 100 | Each enchantment is added with chance `p x 1.5` (certain from p 0.67, about level 38). |
| `mobs.gear.enchants.armor` | `{ protection: 4 }` | level 1 to 255 | Enchantment id to level at p = 1. The level used is `round(p x max)`, at least 1. |
| `mobs.gear.enchants.bow` | `{ power: 5, punch: 2, flame: 1 }` | | Bows. |
| `mobs.gear.enchants.crossbow` | `{ quick_charge: 3, multishot: 1, piercing: 4 }` | | Crossbows. |
| `mobs.gear.enchants.melee` | `{ sharpness: 5, fire_aspect: 2 }` | | Swords, axes and anything else held. |

Default tiers:

| from-level | armor | weapon | armor-chance |
|---|---|---|---|
| 1 | leather | none (vanilla weapon kept) | 0.25 |
| 11 | chainmail | stone | 0.85 |
| 18 | iron | iron | 0.85 |
| 35 | diamond | diamond | 0.85 |
| 45 | netherite | netherite | 0.85 |

Each row is `{ from-level, armor, weapon, armor-chance }` with `from-level` 1 to 1000 and `armor-chance` 0 to 1; a row missing one of these is skipped with a warning. The highest tier at or below the mob's level is used. `armor-chance` is per piece. `armor` must have all four pieces (`<armor>_helmet` and so on), and `weapon` both a sword and an axe; if not, that tier gets no armor or keeps vanilla weapons, with a warning. Bows and crossbows are never replaced. A tier list without `from-level: 1` leaves low-level mobs unarmored. With `drop-chance` above 0, deep mobs drop netherite armor, which skips most of the progression; even `0.01` adds up on a busy server.

### Rewards

Only for kills by players.

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.rewards.xp-max-multiplier` | `10` | 0 to 1000 | XP x `1 + (10 - 1) x p`. |
| `mobs.rewards.extra-loot-max-rolls` | `2` | 0 to 64 | Each drop stack gets `2 x p` extra copies (fraction rounded randomly). Not for elites. |
| `mobs.rewards.rare-drop-max-chance` | `0.03` | 0 to 1 | Chance `0.03 x p` of one pick from `rare-drops`. Not for elites (they get `elites.rare-drops` picks). |
| `mobs.rewards.rare-drops` | see below | rows | The rare-drop table. |
| `mobs.rewards.book-excluded-enchantments` | `[mending]` | enchantment ids | Never put on the random `enchanted_book` rare drops. `[]` = none excluded. An id that is not a valid enchantment id logs a warning and is skipped. |

Default `rare-drops` (weights add up to 100, so weight = percent):

| item | weight | amount |
|---|---|---|
| diamond | 30 | 1 to 2 |
| emerald | 30 | 2 to 4 |
| enchanted_book | 25 | 1 (one random enchantment at a random level, from `#minecraft:on_random_loot` minus `mobs.rewards.book-excluded-enchantments`) |
| golden_apple | 12 | 1 |
| enchanted_golden_apple | 2 | 1 |
| totem_of_undying | 1 | 1 |

The `enchanted_book` roll picks from the vanilla tag `#minecraft:on_random_loot`, the enchantments chest loot can roll. Curses and Frost Walker can still roll; Swift Sneak, Soul Speed, Wind Burst, Mending and every [DwellerEnchants](config-enchants.md) enchantment never do.

Each row is `{ item, weight, min, max }`; `weight` 0 to 1000000, `min` and `max` 1 to 64; `min` defaults to 1 and `max` to `min`. Weights are relative: `{ diamond: 1, emerald: 3 }` gives 25% and 75%. A row with an unknown item or `max` below `min` is skipped with a warning. These numbers are also written out on the player wiki [kill rewards](https://craftyplayz.github.io/DwellerMC-wiki/mobs/rewards/) page.

### Spawn mix

| Key | Default | Range | Effect |
|---|---|---|---|
| `mobs.spawn-mix.replace-chance` | `[0.0, 0.5]` | 0 to 1 each | Chance a natural hostile spawn is swapped, blended from cave top to world bottom. |
| `mobs.spawn-mix.deep-mob-start-y` | `-1400` | -2032 to 15 | Below this Y, swaps can roll a deep mob. |
| `mobs.spawn-mix.deep-mob-chance` | `0.15` | 0 to 1 | Chance a swap below that Y is a deep mob. |
| `mobs.spawn-mix.tough-mobs` | `{ skeleton: 4, witch: 1, cave_spider: 2, vindicator: 2 }` | weight 0 to 1000000 | The normal swap pool; chance = weight / sum. |
| `mobs.spawn-mix.deep-mobs` | `{ piglin_brute: 4, blaze: 3, wither_skeleton: 4, warden: 0.2 }` | weight 0 to 1000000 | The deep swap pool. |

Only `NATURAL` spawns in DwellerCore worlds are swapped; the replacement spawns as `CUSTOM` and then gets leveled normally. A pool needs at least one weight above 0, otherwise the default pool is used. Unknown or non-spawnable ids are skipped with a warning. A warden weight above 1 fills the deepest part of the world with wardens, which most players can only run from. Non-hostile mobs in a pool (for example `cow`) spawn but are not leveled. The player wiki [tougher spawns](https://craftyplayz.github.io/DwellerMC-wiki/mobs/spawn-mix/) page shows both pools.

The [DwellerAdmin](config-admin.md#mobs-page) panel spawns mobs through `adminSpawn` (exact level, no spread, any world, elite chosen by the admin; mobs in `no-scale` and non-hostile mobs get no level) and shows depth, level, elite chance and ore multiplier through `adminInfo`.

## elites

| Key | Default | Range | Effect |
|---|---|---|---|
| `elites.chance` | `[0.01, 0.03]` | 0 to 1 each | Chance a leveled mob is elite, blended from cave top to world bottom. |
| `elites.xp-multiplier` | `5` | 0 to 1000 | Extra XP multiplier for elite kills, on top of `mobs.rewards`. |
| `elites.rare-drops` | `1` | 0 to 64 | Picks from `mobs.rewards.rare-drops` on every elite kill by a player. |
| `elites.bossbar-distance` | `32` | 0 to 256 | Boss bar shows to players within this many blocks. `0` = never. |
| `elites.bossbar-color` | `red` | pink, blue, red, green, yellow, purple, white | |
| `elites.bossbar-style` | `progress` | progress, notched_6, notched_10, notched_12, notched_20 | |
| `elites.bossbar-name` | `<gold>Lv <level> <title> <mob>` | MiniMessage | `<title>` is the adjectives (no trailing space). |
| `elites.particle` | `soul_fire_flame` | particle id | Shown on elites while a player is near. Must be a particle without extra data (not `dust` or `block`). |
| `elites.particle-count` | `12` | 0 to 1000 | Particles per second. `0` = none. |
| `elites.trait-count` | `{ 1: 1, 16: 2, 36: 3 }` | count 0 to 12 | From level: number of traits. The highest from-level at or below the mob's level is used. |

Elites don't glow, because glowing shows through walls; the particles mark them instead. An elite drops one random [DwellerSurface](config-surface.md) suit piece with that plugin's `drops.elite-chance`, which reads the `dwellercore:elite_traits` key. A `trait-count` of 0 for a level range means mobs there that roll elite get no traits, so they are not elites. If fewer traits are enabled than the count, every enabled trait is used.

### Traits

Every trait has `enabled` (false = never picked for new elites) and `name` (the adjective in the elite's name). Existing elites keep the traits and name they spawned with, because both are stored on the mob. `every-seconds` is how often the ability fires (range 1 to 3600); the starting second is random per elite, so elites don't fire in sync.

| Trait | name | Settings (default, range) | Effect |
|---|---|---|---|
| `vampiric` | Vampiric | `heal-fraction` 0.3 (0 to 10) | Heals 30% of the damage it deals. |
| `thorns` | Thorny | `reflect-fraction` 0.2 (0 to 10) | A player hitting it takes 20% of the damage back (from arrows too, to the shooter). It reflects the damage from before DwellerEnchants' bonuses (DwellerCore handles the hit at `HIGH`, DwellerEnchants adds its bonuses at `HIGHEST`). |
| `berserker` | Berserk | `max-damage-multiplier` 2 (1 to 100) | Damage x1 at full health, rising linearly to x2 at 0 health. |
| `shielded` | Shielded | `hits` 3 (0 to 1000), `pierced-by` `["dwellerenchants:elite_bane", "dwellerenchants:executioner"]` | The first 3 hits are blocked completely, with a shield sound. A player whose main-hand weapon has an enchantment listed in `pierced-by` hits through the shield (also with arrows and tridents they shot) and uses no charge. Ids of enchantments that don't exist are skipped silently (the defaults come from [DwellerEnchants](config-enchants.md)); a malformed id logs a warning. `[]` = nothing pierces. |
| `blink` | Blinking | `every-seconds` 8, `range` 16 (1 to 128), `distance-behind` 2 (0 to 16) | Teleports behind its target when within range. |
| `leaper` | Leaping | `every-seconds` 4, `min-distance` 4, `max-distance` 12 (0 to 64), `lift` 0.5 (0 to 5) | Jumps at its target. |
| `webber` | Webbing | `every-seconds` 10, `web-ticks` 100 (1 to 72000) | Puts a cobweb at the target's feet (only in air), removed after 5 s. |
| `burrower` | Burrowing | `every-seconds` 2, `min-distance` 2 (0 to 64), `max-hardness` 50 (0 to 3600000) | Digs a 1x2 hole toward a target it can't see. |
| `summoner` | Summoning | `every-seconds` 15, `per-wave` 2 (0 to 20), `max-alive` 6 (0 to 50), `spread` 2 (0 to 16) | Spawns copies of itself near it. |
| `frost_aura` | Frostbound | `every-seconds` 1, `radius` 6 (0 to 64), `slowness-ticks` 40 (1 to 72000), `slowness-amplifier` 0 (0 to 9) | Slowness I on nearby players. |
| `darkness_pulse` | Shrouded | `every-seconds` 10, `radius` 10 (0 to 64), `darkness-ticks` 60 (1 to 72000) | Darkness on nearby players. |
| `explosive_death` | Explosive | `power` 3 (0 to 20), `fire` false, `break-blocks` false | Explodes on death (power 3 = creeper, 4 = TNT). |

Every trait, Explosive death included, is off while the mob is silenced ([`mobs.silence-respected`](#levels-and-stats)). Silence comes from [DwellerEnchants](config-enchants.md).

**burrower.** Never breaks unbreakable blocks (bedrock, so it can't dig through the roof), liquids or blocks with contents (chests, spawners, any tile entity). Blocks harder than `max-hardness` are skipped: stone is 1.5, deepslate 3, obsidian 50, so the default digs through everything except bedrock-like blocks. Set `2` to keep burrowers out of deepslate. It breaks player builds outside protected areas; [WorldGuard](plugins/worldguard.md) regions don't stop it, because the block change is not a player action.

**summoner.** Copies are full mobs of the same type and get leveled (and can roll elite themselves). `max-alive` above 10 with short `every-seconds` floods a cave quickly.

**explosive_death** with `break-blocks: true` lets elites destroy builds and chests near where they die; [CoreProtect](plugins/coreprotect.md) logs it as an explosion. `fire: true` sets fire to wood nearby.

**webber** with long `web-ticks` leaves cobwebs everywhere players fight; they are only removed if still a cobweb when the timer ends.

The player wiki [elite mobs](https://craftyplayz.github.io/DwellerMC-wiki/mobs/elites/) page describes every trait with these numbers.

See also: [Technical notes](technical.md), [Troubleshooting](troubleshooting.md), [DwellerBiomes config](config-biomes.md), [DwellerStructures config](config-structures.md), [DwellerSurface config](config-surface.md), [DwellerEnchants config](config-enchants.md).

# DwellerCore config

File: `plugins/DwellerCore/config.yml` (default: `DwellerCore/src/main/resources/config.yml`).

**When changes apply:** every key needs a server restart (there is no reload command). Keys marked **new chunks** only change chunks generated after the restart. Mob keys only change mobs that spawn after the restart; existing mobs keep the stats they spawned with.

Mob scaling uses **power** `p = (hidden / 5000) ^ curve-exponent`, where `hidden = (level - 1) * 100 + random 1..100` (`MobLevels.java`). `p` is about 0 at level 1 and about 1 at level 50.

## world

| Key | Default | Used in | Effect |
|---|---|---|---|
| `world.cave-top-y` | `0` | `Depth.java` | Y where depth is 0 and area level is 1. Used by the HUD, mob levels, spawn mix and elite chance. Does **not** move the roof or caves (those are fixed in `DwellerGenerator.java`). |

## caves (new chunks)

| Key | Default | Used in | Effect |
|---|---|---|---|
| `caves.biome-boost` | `{}` | `DwellerGenerator.java`, `CaveNoise.java` | Map of biome key → extra cave density, e.g. `{ "minecraft:lush_caves": 0.12 }`. Added to the room and huge-void noise (not tunnels). Uses the biome from DwellerBiomes' provider; empty map = no biome lookups. |

## spawn

| Key | Default | Used in | Effect |
|---|---|---|---|
| `spawn.random-cave` | `false` | `SpawnListener.java` | `true`: first join and bed-less respawns go to a random cave (below). `false`: left to the server spawn, which is [EssentialsSpawn](plugins/essentials.md) `/setspawn` on this server. Missing from older config files, where the code default is `true`. |
| `spawn.min-y` | `-600` | `SpawnListener.java` | Lowest Y scanned for a spawn spot. |
| `spawn.max-y` | `-300` | `SpawnListener.java` | Highest Y scanned. |
| `spawn.radius` | `256` | `SpawnListener.java` | Random X/Z offset from the world spawn. |

**Beds always win**, whatever `random-cave` is set to: DwellerCore notes a bed or anchor respawn location at priority LOWEST and restores it at HIGHEST, so EssentialsSpawn or another plugin can't move it.

With `random-cave: true`, used for first join and for respawns without a bed or respawn anchor: 8 random columns are scanned top-down for a solid, non-magma floor with 2 air blocks above. If none is found, a 3×3×3 room with a deepslate floor is carved at the middle Y.

## hud

| Key | Default | Used in | Effect |
|---|---|---|---|
| `hud.enabled` | `true` | `HudTask.java` | Action bar once per second: `Depth 1,240 · Area Lv 30`. |

## ores (new chunks)

| Key | Default | Used in | Effect |
|---|---|---|---|
| `ores.buried-chance` | `0.1` | `OrePlacer.java` | Ore blocks with no face touching air are placed at this rate. Faces on the chunk edge don't count as touching air. |
| `ores.depth-multiplier` | `[[-400, 1], [-1000, 2], [-2032, 3]]` | `OrePlacer.java` | `[y, multiplier]` points, blended linearly, flat above the top point and below the bottom one. |
| `ores.types.<name>.veins` | see below | `OrePlacer.java` | Veins per 16×16×64 slab, times the multiplier at the slab's middle Y. Fractions are rounded randomly. Must be a single number or the entry is skipped with a warning. |
| `ores.types.<name>.size` | see below | `OrePlacer.java` | Blocks per vein (random walk). Default 4 if missing. |

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

`<name>` maps to `<name>_ore` in stone and `deepslate_<name>_ore` in deepslate. If `<name>_ore` doesn't exist, `<name>` itself is tried (e.g. `ancient_debris`), and the same block is used in deepslate. Ores only replace `STONE` and `DEEPSLATE`.

## mobs

Applies to hostile mobs (`Enemy`) in DwellerCore worlds, except types in `no-scale`.

| Key | Default | Used in | Effect |
|---|---|---|---|
| `mobs.max-level` | `50` | `Depth.java`, `MobLevels.java` | Area level at the world bottom. Area level = `1 + round(depthFraction × (max-level − 1))`. |
| `mobs.level-spread` | `2` | `MobLevels.java` | Mob level = area level ± this (random), clamped to 1..max-level. |
| `mobs.curve-exponent` | `1.32` | `MobLevels.java` | Exponent in the power formula. |
| `mobs.max-health-multiplier` | `6.0` | `MobLevels.java` | Health × `1 + (6 − 1) × p`. |
| `mobs.max-damage-multiplier` | `3.0` | `MobLevels.java` | Attack damage × `1 + (3 − 1) × p`. |
| `mobs.max-speed-bonus` | `0.4` | `MobLevels.java` | Speed × `1 + 0.4 × p`. |
| `mobs.max-armor` | `12` | `MobLevels.java` | `+12 × p` armor. |
| `mobs.max-toughness` | `6` | `MobLevels.java` | `+6 × p` armor toughness. |
| `mobs.follow-range-min` / `-max` | `16` / `48` | `MobLevels.java` | Follow range = max(vanilla, `16 + 32 × p`). |
| `mobs.name-tag-distance` | `12` | `MobDisplayTask.java` | Name tag (`Lv N Name ❤ hp/max`) shows within this many blocks of a player. |
| `mobs.no-scale` | `[WARDEN, WITHER, ENDER_DRAGON, ELDER_GUARDIAN]` | `MobLevels.java`, `SpawnMix.java` | Never leveled, never swapped by spawn mix. |
| `mobs.status-hit-max-chance` | `0.35` | `MobPerks.java` | Chance per hit on a player = `0.35 × p`: random Slowness, Weakness, Hunger, Poison or Mining Fatigue for 3–6 s, level II at mob level 35+. |
| `mobs.rewards.xp-max-multiplier` | `10` | `MobRewards.java` | XP × `1 + (10 − 1) × p`. Player kills only. |
| `mobs.rewards.extra-loot-max-rolls` | `2` | `MobRewards.java` | Each drop stack gets `2 × p` extra copies (fraction rounded randomly). Not for elites. |
| `mobs.rewards.rare-drop-max-chance` | `0.03` | `MobRewards.java` | Chance `0.03 × p` of one rare drop. Not for elites (they always get one). |
| `mobs.spawn-mix.replace-chance` | `[0.0, 0.5]` | `SpawnMix.java` | Chance a natural hostile spawn is swapped, blended from cave top to world bottom. |
| `mobs.spawn-mix.deep-mob-start-y` | `-1400` | `SpawnMix.java` | Below this Y, swaps can roll a deep mob. |
| `mobs.spawn-mix.deep-mob-chance` | `0.15` | `SpawnMix.java` | Chance a swap below that Y is a deep mob. |

Hardcoded mob rules (not in config, `MobLevels.java`, `MobGear.java`, `MobPerks.java`, `MobDisplayTask.java`):

- Knockback resistance `+0.5 × p`. Zombie reinforcement chance `+0.5 × p`. Piglins never zombify.
- Creepers: explosion radius `3 + level / 12`, fuse `30 − 15 × p` ticks.
- Gear for zombies, skeletons, piglins, vindicators, pillagers. Tier by level: <11 leather/wooden, 11+ chainmail/stone, 18+ iron, 35+ diamond, 45+ netherite. Gear never drops.
- Spawn-mix swap pool: skeleton 4, witch 1, cave spider 2, vindicator 2. Deep pool: piglin brute 4, blaze 3, wither skeleton 4, warden 0.2.
- Witches level 25+ throw strong potions. Slimes split into `+level / 15` extra. Skeletons and pillagers aim better with `p`.

## elites

| Key | Default | Used in | Effect |
|---|---|---|---|
| `elites.chance` | `[0.01, 0.03]` | `EliteManager.java` | Chance a leveled mob is elite, blended from cave top to world bottom. |
| `elites.xp-multiplier` | `5` | `MobRewards.java` | Extra XP multiplier for elite kills. |
| `elites.bossbar-distance` | `32` | `EliteManager.java` | Boss bar shows to players within this many blocks. |

Elites get 1 trait at level ≤15, 2 at ≤35, 3 above. Traits: Vampiric, Thorny, Berserk, Shielded, Blinking, Leaping, Webbing, Burrowing, Summoning, Frostbound, Shrouded, Explosive. Burrowing elites break blocks with hardness 0–50 that aren't tile entities.

## Hardcoded world values

| Value | Where |
|---|---|
| World bottom Y -2032 (datapack), top Y 15 | `Depth.java`, `dimension_type/overworld.json` |
| Roof band Y -16..0 left solid, air above Y 0 | `DwellerGenerator.java` |
| Caves only below Y -17, fading to solid over the top 16 blocks | `DwellerGenerator.java` |
| Stone above Y -300, deepslate below, 8-block mixed band | `DwellerGenerator.java` |
| Bedrock floor at Y -2032 plus 4 thinning layers | `DwellerGenerator.java` |

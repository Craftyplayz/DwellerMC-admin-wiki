# DwellerSurface config

File: `plugins/DwellerSurface/config.yml` (default: `DwellerSurface/src/main/resources/config.yml`). Runtime state lives in `plugins/DwellerSurface/data.yml` ([see below](#datayml)).

**When changes apply:** a full **restart**. There is no reload command and the config is read once at startup, messages included.

## What it does

The underground has a bedrock roof, so players never see the sky. DwellerSurface adds one extra world, the **surface**: a flat desert at one fixed time of day, far too hot to live in. Players reach it through a single portal that you build yourself, and only while wearing a four-piece suit whose durability is a timer. When the timer runs out the pieces crumble and the wearer burns to death within seconds. Hostile mobs of level 50 hunt them on the way. Surface-only loot (a daily item) and the suit itself give players a reason to go.

The loop:

1. Players kill elite mobs (DwellerCore) and get a small chance at a suit piece.
2. With all four pieces worn they walk into the underground portal and arrive on the surface.
3. They have `suit.wear-seconds` (default 30 minutes) of worn time to do what they came for and walk into the surface portal to return.
4. A minute after the last player leaves, the whole world is unloaded.

## Install

1. Build the plugin: `./gradlew build` in `DwellerSurface/`. The jar is `build/libs/DwellerSurface-<version>.jar`.
2. Copy it into `plugins/`. Needs Paper 26.3 and Java 25. It is a Paper plugin (`paper-plugin.yml`) and lists DwellerCore as an optional dependency that loads before it.
3. Start the server once so `config.yml` is written. Nothing else is generated: the surface world is created the first time someone goes up.
4. DwellerCore is optional but recommended. Without it the surface mobs have no level and no mob is an elite, so suit pieces only come from `drops.mob-chances` or `/surface give`.

### First-time setup

The portals are areas you build and mark yourself. Until both boxes are set, walking around does nothing.

1. Build the **underground portal** somewhere in the cave world (any frame or room, it is only decoration). Stand inside it on the floor and run `/surface setportal down [radius] [height]`.
2. Pick the spot where players land after coming back, **outside** the portal box. Stand there and run `/surface setarrival down`. If you skip this, the main world's spawn point is used.
3. Run `/surface tp`. This loads (the first time, creates) the surface world and puts you at the surface arrival point. Until an arrival is set, that is the world spawn: the border centre, on top of the flat layers.
4. On the surface, build the **surface portal**. Stand inside it and run `/surface setportal up [radius] [height]`.
5. Stand where players should land on the surface, outside that box, and run `/surface setarrival up`. The daily item also appears here.
6. Run `/surface` to check both boxes and both arrivals.

!!! warning "Keep each arrival outside its portal boxes"
    Arrivals are not offset. An arrival inside a portal box makes the player trigger that portal on landing. Only `portals.cooldown-seconds` stops an instant trip back, and once it ends the player is sent again as soon as they move to a new block in the box. Stand a few blocks away from the box before running `setarrival`.

The box is a column: `radius` blocks to each side of where you stand, and `height` blocks up starting at your feet. Defaults: radius 1, height 3, so 3 x 3 wide and 3 tall. Radius 0 is one block wide. The box is stored as absolute block coordinates, so if you move the build, set the box again.

`setportal up` and `setarrival up` only work while you are in the surface world, `down` only outside it. Otherwise you get `'up' must be set on the surface (/surface tp)` or `'down' must be set underground.`

## Commands and permissions

One command, `/surface`, permission `dwellersurface.admin`. Feedback to admins is fixed text, not part of `messages`.

| Command | Effect |
|---|---|
| `/surface` or `/surface info` | World state (loaded with player count, or unloaded), both portal boxes and arrivals, the date the daily item was last handed out and whether one is waiting. Works from the console. |
| `/surface setportal <down\|up> [radius] [height]` | Marks the box around your position. Radius and height default to `portals.default-radius` and `portals.default-height`. Players only. |
| `/surface setarrival <down\|up>` | Marks your exact position, yaw and pitch as the arrival point. Players only. |
| `/surface give <player> [helmet\|chestplate\|leggings\|boots\|all]` | Gives suit pieces (default `all`). The player must be online. What does not fit drops at their feet. |
| `/surface daily` | Spawns the daily item now at the surface arrival point and broadcasts `daily-spawned`. Loads the world. Does not change `daily.last-date`. |
| `/surface tp` | Teleports you to the surface arrival point. The plugin's own teleport, so the teleport guard does not stop it. Players only. |

| Permission | Default | Effect |
|---|---|---|
| `dwellersurface.admin` | op | Use `/surface`. |
| `dwellersurface.bypass` | nobody | Enter the surface without the suit (`portals.require-suit`), never burn (`burn.*`), and teleport into the surface with any command (`portals.block-other-teleports`). It does not stop a worn suit from draining, and does not skip the respawn point block. Meant for staff. |

Players in creative or spectator are treated like bypass for the suit check and the burn, and their suit does not drain while in those modes.

## world

| Key | Default | Unit | Effect |
|---|---|---|---|
| `world.name` | `world_surface` | world name | Name and folder of the surface world, in the server root next to `world`. Created on first use. Renaming makes a new empty world; the old folder stays on disk. The name must match [`mobs.fixed-level-worlds`](config-core.md#mobs) in DwellerCore or the mobs there get no level. The stored portal boxes and arrivals in `data.yml` keep the old world name, so set the `up` ones again. |
| `world.unload-delay-seconds` | `60` | seconds | The world is unloaded once it has been empty this long. Checked every 10 seconds, so the real delay can be up to 10 s longer. `-1` = never unload. Too long wastes RAM and chunk ticking; too short means a 1 to 3 second hitch for the next person to go up. |
| `world.border-size` | `2000` | blocks | Side length of the square world border. Applied on every load. A border too small leaves arrival points outside it. |
| `world.center-x`, `world.center-z` | `0`, `0` | block coordinates | Border centre. Also the world spawn column, used as the fallback arrival. |
| `world.base-y` | `-100` | Y level | Y of the bottom block of the first layer. The sky ends at Y 15 (see below), so -100 leaves about 110 blocks of building room above the sand. Higher values mean less room; 0 leaves about 11 blocks. |
| `world.layers` | bedrock x1, sand x3 | list | Flat layers from `base-y` upward, each `{ block: <material>, height: <n> }`. With the defaults the layers fill Y -100..-97 and the world spawn is at Y -96. Invalid entries (unknown block, not a block, height under 1) are skipped with a log warning. Only affects chunks generated afterwards. |
| `world.biome` | `minecraft:desert` | biome id | One biome for the whole world: sky and grass colour, fog, music. Datapack biomes work. An unknown id logs a warning and falls back to desert. Only affects chunks generated afterwards. |
| `world.time` | `6000` | ticks | Time of day, frozen (the day cycle is off). 0 sunrise, 6000 noon, 12000 sunset, 18000 midnight. Set on every load. |
| `world.weather` | `false` | boolean | `false` forces clear weather and stops the weather cycle. `true` lets vanilla weather run. |

The world has no caves, structures, decorations or generated mobs, and vanilla natural mob spawning is off (the spawn mobs game rule). [`mobs`](#mobs) spawns them instead. Everything above the layers is air.

!!! note "Height of the surface world"
    The surface world uses the overworld dimension type, which DwellerCore's datapack sets to start at Y -2032 and be 2048 blocks tall (Y -2032 to 15). The flat layers sit at `base-y` (default -100), so there are about 110 blocks of air above the sand and a deep void below the bedrock. Keep the layers inside Y -2032..15; nothing can be built above Y 15. A player who falls below the layers drops into the void and dies.

## portals

| Key | Default | Unit | Effect |
|---|---|---|---|
| `portals.default-radius` | `1` | blocks | Radius used by `/surface setportal` when none is given. 1 = 3 x 3. |
| `portals.default-height` | `3` | blocks | Height used by `/surface setportal` when none is given. |
| `portals.cooldown-seconds` | `3` | seconds | After using a portal a player cannot use either portal for this long. Stops landing in a box and bouncing. `0` disables it. |
| `portals.message-cooldown-seconds` | `3` | seconds | Gap between `need-suit` messages while someone stands in the underground portal without the suit. Low values spam chat. |
| `portals.require-suit` | `true` | boolean | Going up needs the right suit piece in each of the four armor slots, each with time left. Bypass, creative and spectator skip it. If `false`, anyone can go up, and anyone without a suit burns there. |
| `portals.block-other-teleports` | `true` | boolean | A teleport into the surface that did not come from the portal or `/surface tp` is cancelled and `teleport-blocked` is shown. Covers `/home`, `/back`, `/tpa`, warps and any other plugin teleport. Moves within the surface and out of it are never blocked. If `false`, players can skip the suit check by teleporting. |
| `portals.block-respawn-point` | `true` | boolean | Beds and respawn anchors on the surface do not set the respawn point (`respawn-blocked`). |

A player is sent through when they step onto a new block inside the box, so the portal must be walkable.

## suit

Four pieces, each a normal armor item with a plugin tag. The durability bar is the timer: `wear-seconds` of durability, one point lost per second worn.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `suit.wear-seconds` | `1800` | seconds | Worn time per piece (its max durability, minimum 1). Only affects pieces made afterwards; existing ones keep their old maximum. |
| `suit.drain-only-on-surface` | `false` | boolean | `false`: pieces lose a second for every second worn anywhere, so players must not wear them underground. `true`: only drains in the surface world. Creative and spectator never drain. |
| `suit.ignore-combat-wear` | `true` | boolean | `true`: hits, thorns and explosions do not wear the suit, so it lasts exactly `wear-seconds`. `false`: normal armor wear on top of the timer. |
| `suit.blocked-enchantments` | `[mending, unbreaking, "dwellerenchants:unbreakable"]` | list of enchantment ids | Cannot be put on suit pieces (enchanting table and anvil; an anvil result containing one is cleared). Unknown ids log a warning. `dwellerenchants:unbreakable` is [DwellerEnchants](config-enchants.md)' Unbreakable; without that plugin the id is unknown and logs a warning, and everything else works. If `mending` is listed, Mending also cannot repair the piece. |
| `suit.block-repair` | `true` | boolean | No repair at all: anvil, grindstone, crafting grid and Mending. `false` allows them, and a repaired piece gets its time back. |
| `suit.warn-at-seconds` | `[300, 60, 10]` | seconds | `suit-warning` is sent when a worn piece reaches exactly one of these values. Empty = no warnings. |
| `suit.pieces.<slot>.material` | netherite armor | item id | Item for that slot (`helmet`, `chestplate`, `leggings`, `boots`). Use an item that can be worn in that slot. An unknown or non-item id logs `Invalid or missing material` and that piece does not exist, so nobody (except bypass) can ever have a full suit. |
| `suit.pieces.<slot>.name` | `<gold>Sunward ...` | MiniMessage | Display name. |
| `suit.pieces.<slot>.lore` | two grey lines | list of MiniMessage | Lore lines. |

When a worn piece reaches 0 it is removed from the armor slot with `suit-broken` and a break sound. Pieces are recognised by a plugin tag, not by name, so an item renamed in an anvil is still a piece and a look-alike is not. Changing `material`, `name` or `lore` only affects pieces made afterwards.

## drops

| Key | Default | Unit | Effect |
|---|---|---|---|
| `drops.elite-chance` | `0.02` | chance 0 to 1 | Chance an elite mob drops one random suit piece. An elite is a mob with DwellerCore's persistent data key `dwellercore:elite_traits`. |
| `drops.mob-chances` | `{}` | mob id to chance | Same drop for named mob types, for example `{ warden: 0.25, wither: 1.0 }`. For an elite of a listed type the higher of the two chances is used. Unknown ids log a warning and are skipped. |
| `drops.require-player-kill` | `true` | boolean | Only count kills with a player as killer. |

The piece is chosen uniformly from the four. This applies to elite mobs in every world, not only the surface. Players never drop suit pieces this way. With `elite-chance: 0` and an empty `mob-chances` the only source is `/surface give`.

## burn

Applies to players in the surface world who are not exempt (creative, spectator, bypass) and do not wear a full suit with time left.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `burn.grace-seconds` | `3` | seconds | Seconds between the burn starting (the `burn-title` shows) and the first damage. |
| `burn.seconds-to-death` | `15` | seconds | Burn time after the grace period until death (minimum 1). Each second deals max health divided by this, ignoring armor, Fire Resistance, enchantments and totems. The last second kills outright, so healing only delays the damage. Time on the surface without a suit is grace plus this. |
| `burn.visual-fire` | `true` | boolean | Show flames on burning players. Removed when they put the suit on, leave or die. |

Wearing a full suit again resets the counter.

## daily-item

One item per real day, placed at the surface arrival point. It never despawns, mobs cannot pick it up, and picking it up broadcasts `daily-claimed`.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `daily-item.enabled` | `true` | boolean | Turns the feature off. |
| `daily-item.time` | `"18:00"` | `HH:mm`, 24 h | Time of day it appears. Checked every 30 seconds. Keep the quotes. |
| `daily-item.timezone` | `""` | zone id | For example `Europe/Amsterdam`. Empty = the server's time zone. A bad time or zone logs `Invalid daily-item.time or timezone` and the feature does nothing. |
| `daily-item.glow` | `true` | boolean | The dropped item glows. |
| `daily-item.item.material` | `nether_star` | item id | Item type. An unknown id logs a warning and nothing spawns (`/surface daily` then says the config is broken). |
| `daily-item.item.amount` | `1` | count | Stack size. |
| `daily-item.item.name`, `.lore` | Sun Shard | MiniMessage | Name and lore. |

At the set time the plugin writes the date to `data.yml`. If the surface is loaded the item spawns at once. If not, `daily.pending` is set and the item spawns the next time the world loads, which is when the first player goes up. A missed day is not given twice. Uncollected items stay, so they can pile up over several days. The default item is a placeholder: set `daily-item.item` to what you want to hand out.

## mobs

Surface mobs are spawned by the plugin. Each player on the surface (spectators excluded) gets one attempt every `interval-seconds`.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `mobs.enabled` | `true` | boolean | Turns surface spawning off. |
| `mobs.interval-seconds` | `10` | seconds | Gap between attempts. Minimum 1. |
| `mobs.spawn-chance` | `0.5` | chance 0 to 1 | Chance an attempt spawns anything. |
| `mobs.max-near-player` | `4` | count | No spawn if this many hostile mobs are already within `near-radius` of the player. |
| `mobs.near-radius` | `48` | blocks | Radius of that count (a cube). |
| `mobs.min-distance`, `mobs.max-distance` | `16`, `40` | blocks | The mob appears at a random angle and a distance in this range, on the highest block of that column. Spots outside the world border are skipped. |
| `mobs.types` | `husk 4, parched 3, blaze 2, magma_cube 2` | mob id to weight | Weighted pick, ids as in `/summon`. Unknown, non-spawnable or weight 0 or less are skipped with a warning. If none are left, spawning is off. |

It is always noon, so mobs that burn in daylight (zombie, skeleton) burn. Husks and parched do not. The cap counts all nearby hostile mobs, so players standing together share it.

Level and stats come from [DwellerCore](config-core.md#mobs): the world is in `mobs.fixed-level-worlds` (default level 50), so these mobs are level 48 to 50 (level-spread 2).

## messages

All are MiniMessage. An empty string `""` turns that message off.

| Key | When | Placeholders |
|---|---|---|
| `need-suit` | Standing in the underground portal without the suit | none |
| `teleport-blocked` | A teleport into the surface was cancelled | none |
| `respawn-blocked` | A bed or anchor on the surface was refused | none |
| `arrived-surface` | Landing on the surface | none |
| `arrived-underground` | Landing underground | none |
| `suit-timer` | Action bar every second on the surface while protected | `<time>` shortest time left, m:ss |
| `suit-warning` | A worn piece reached a `warn-at-seconds` value | `<piece>`, `<time>` |
| `suit-broken` | A worn piece reached zero | `<piece>` |
| `burn-title`, `burn-subtitle` | Title and subtitle when the burn starts | none |
| `burn-death` | Death message, if the player died after the grace period | `<player>` |
| `daily-spawned` | Broadcast when the daily item appears | none |
| `daily-claimed` | Broadcast when someone picks it up | `<player>` |

## data.yml

`plugins/DwellerSurface/data.yml` is runtime state written by the plugin, not configuration. Do not edit it while the server runs; it is saved again on stop. Copy it along with the world folders if you move the server.

```yaml
portals:
  down:
    box: world,10,-500,20,12,-498,22           # world,x1,y1,z1,x2,y2,z2
    arrival: world,15.5,-500.0,20.5,90.0,0.0   # world,x,y,z,yaw,pitch
  up:
    box: world_surface,0,4,0,2,6,2
    arrival: world_surface,5.5,4.0,0.5,0.0,0.0
daily:
  last-date: 2026-11-15   # date the item was last handed out
  pending: false          # true = waiting for the next world load
```

Boxes and arrivals are plain strings, so a world that is not loaded never breaks loading. An arrival whose world is not loaded counts as not set. Removing `daily.last-date` lets the item appear again today.

## How the world loads and unloads

- The surface world is not loaded at startup. The first person to enter creates it with a Bukkit `WorldCreator` and the plugin's flat generator, on the main thread. Expect a **1 to 3 second hitch**. The log prints `Loaded surface world in N ms`. Border, time, weather and the spawn rule are applied on every load.
- Every 10 seconds the plugin checks. After `unload-delay-seconds` without players the world is saved and unloaded (`Unloaded surface world (empty)`). A failed unload logs a warning and is retried.
- On shutdown anyone still up there is moved to the underground arrival. A player who logs out on the surface is moved there first too, because the world may be unloaded when they return.
- Blocks, containers and items on the ground are saved with the world and are there when it loads again. Nothing ticks while it is unloaded: no mobs, no burn, no item despawn.

## Interactions

**DwellerCore.** The surface is not a DwellerCore world, so its generator, HUD and depth rules do not apply. Mob levels come through [`mobs.fixed-level-worlds`](config-core.md#mobs) (default `world_surface: 50`). Elites there roll the chance of the world bottom. Elites carry the key `dwellercore:elite_traits`, which is what the suit drop checks. Without DwellerCore the key never exists.

**EssentialsX.** `/home`, `/back`, `/tpa`, `/tpahere`, `/warp` and similar are cancelled into the surface unless the player has `dwellersurface.bypass`. Leaving the surface by these is never blocked. See [EssentialsX](plugins/essentials.md).

**Beds.** `block-respawn-point` stops beds and anchors on the surface from setting a respawn point.

**Protection plugins.** [WorldGuard](plugins/worldguard.md) and [CoreProtect](plugins/coreprotect.md) treat it like any other world. Protect both portal areas if players can build.

## Common problems

| Symptom | Cause | Fix |
|---|---|---|
| Walking into the portal does nothing | Box not set, or set in the wrong world | Run `/surface` and check the box lines. Set it again standing inside the portal. |
| Player bounces between portals | Arrival inside a portal box | Move the arrival outside the box and set it again. Keep `portals.cooldown-seconds` above 0. |
| `need-suit` shown, player can't enter | Missing piece, wrong slot, or a piece at 0 | All four slots need the plugin's pieces with time left. Test with `/surface give <player>`. |
| Suit gone long before 30 minutes | `drain-only-on-surface` is `false` and it was worn underground | Tell players to put it on at the portal, or set it to `true`. |
| Surface mobs have no level | `world.name` and the DwellerCore `mobs.fixed-level-worlds` entry differ, or DwellerCore is missing | Make the names match and restart both. |
| No suit pieces ever drop | DwellerCore missing, `elite-chance` is 0, or the elite was not killed by a player | Check `drops.*`. |
| Log `Invalid or missing material at suit.pieces...` | Typo in a material | Fix and restart. Until then only bypass can enter. |
| Log `Invalid daily-item.time or timezone` | Time is not `HH:mm`, or unknown zone | Fix the value, keep it quoted. |
| 1 to 3 second freeze when someone goes up | The world was unloaded | Normal. Raise `unload-delay-seconds` or use `-1`. |
| `/home` or `/tpa` to the surface refused | `block-other-teleports` | Intended. Staff use `dwellersurface.bypass`. |
| Surface is empty after renaming `world.name` | A new world was made | Change the name back, or set the portals again in the new world. |
| Changed `layers` or `biome`, nothing changed | Existing chunks keep their generation | Stop the server, delete the surface world folder, start again. Everything built there is lost. |

## Links

- [DwellerCore mob settings](config-core.md#mobs)
- [Install](install.md)
- [Troubleshooting](troubleshooting.md)
- [MiniMessage format](https://docs.advntr.dev/minimessage/format.html)
- [Paper documentation](https://docs.papermc.io/)

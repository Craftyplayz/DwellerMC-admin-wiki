# DwellerAdmin config

File: `plugins/DwellerAdmin/config.yml` (default: `DwellerAdmin/src/main/resources/config.yml`). Runtime state lives in `plugins/DwellerAdmin/data.yml` ([see below](#datayml)).

**When changes apply:** a full **restart**. There is no reload command and the config is read once at startup, messages included.

## What it does

DwellerAdmin is an in-game admin panel. `/admin` opens a chest menu with five pages: [Items](#items), [Depth & world](#depth-world), [Mobs](#mobs-page), [Players](#players) and [Server](#server-page). Every click in a panel is cancelled, so nothing can be taken out of a menu by accident. Text that players can see (freeze, restart and maintenance messages, the broadcast format) is in `messages:`. The panel's own labels are fixed.

It has no datapack. It loads normally, not at `STARTUP`, and needs no `bukkit.yml` entry. DwellerCore, DwellerTools, DwellerEnchants and DwellerSurface are optional and load first when installed. The panel asks them for items, info lines and actions through [hooks](#hooks), so a missing plugin only removes its part of the panel.

What players see of the Server page (restart warnings, the broadcast, the maintenance kick, being frozen): [Restarts and announcements](https://craftyplayz.github.io/DwellerMC-wiki/plugins/server-notices/).

## Install

1. Build: `./gradlew build` in `DwellerAdmin/`. The jar is `build/libs/DwellerAdmin-<version>.jar`.
2. Copy it into `plugins/`. Needs Paper 26.3 and Java 25.
3. Restart. `config.yml` appears in `plugins/DwellerAdmin/`. `data.yml` appears after the first state change.
4. Ops can use `/admin` at once. Give anyone else the permission below.

## Command and permission

| Command | Permission | What |
|---|---|---|
| `/admin` | `dwelleradmin.use` | Opens the main menu. In-game only; the console gets a message saying so. |

| Node | Default | Effect |
|---|---|---|
| `dwelleradmin.use` | `op` | Opens the panel and keeps its clicks working (a player who loses it with a menu open has the menu closed on the next click). Also: sees vanished admins, and is not kicked when maintenance turns on. |

Give it only to people you trust with everything below: spawning any item, reading and editing any inventory, restarting the server.

!!! warning "Maintenance only lets in ops"
    The permission is not loaded yet when a player logs in, so the maintenance check at login looks at op status only. An admin who has `dwelleradmin.use` through LuckPerms but is not op is refused during maintenance. Make the people who must get in op, or do not use maintenance. The kick of players who are already online does use the permission.

## world

Settings for the Depth & world page. Depth and spawn teleports use the **cave world**: the world you stand in when DwellerCore generates it, otherwise the first world on the server.

| Key | Default | Effect |
|---|---|---|
| `world.depth-step` | `256` | Blocks between the stops of "Teleport to depth". The stops are Y 0 and every step below it, then the bottom of the world. Values under 1 act as 1. Useful range 16 to 2048. Each page holds 45 stops, so `16` on the 2032-block world gives 128 stops over 3 pages. |
| `world.max-teleport-y` | `-17` | Highest feet Y a panel teleport may end on. `-17` keeps you under the roof band (Y -16 to 0), so the "Y 0" stop lands in the highest cave. Range -2031 to 13. At `0` or higher the Y 0 stop can put you on the roof band. |
| `world.safe-search.vertical` | `32` | Blocks up and down from the target Y that are tried in each column. 0 to 256. |
| `world.safe-search.radius` | `8` | Square rings of columns tried sideways around the target X/Z before giving up. 0 to 32. The chunks in this radius are loaded first without blocking the server, but the search itself runs inside one tick. Radius 32 with vertical 256 can cause a short lag spike. |
| `world.safe-search.unsafe-blocks` | magma block, campfire, soul campfire, fire, soul fire, cactus, pointed dripstone, powder snow, sweet berry bush, wither rose, cobweb | Blocks you are never put on or in. Block ids as in `/setblock`. Lava, water and every other liquid are always avoided. An unknown id logs a warning and is skipped. |
| `world.structure-search-radius` | `100` | Chunks searched around you for "Nearest structure". 1 to 500. Runs on the main thread: the whole server waits. See [limits](#known-limits). |

How a safe spot is found: the target Y is clamped between the bottom of the world plus 1 and `max-teleport-y`. In the target column it looks for a solid block to stand on with two passable blocks above, trying Y, then one above, one below, two above and so on up to `vertical`. Then it does the same in the rings around it. Finding nothing gives a red message with the numbers used. Your facing is kept.

## mobs {#mobs-config}

| Key | Default | Effect |
|---|---|---|
| `mobs.spawn-types` | zombie, husk, drowned, skeleton, stray, spider, cave_spider, creeper, enderman, witch, slime, magma_cube, vindicator, pillager, piglin_brute, blaze, wither_skeleton, warden | Mobs on the "Spawn mob" page, in this order. Ids as in `/summon`. Anything that is not a spawnable living mob logs `not a spawnable mob` and is skipped. |
| `mobs.levels` | `[1, 11, 18, 25, 35, 45, 50]` | Level buttons on the spawn screen. At most 9 are shown. DwellerCore clamps the level to 1 up to its `mobs.max-level` (50 by default), so higher numbers just give the top level. The defaults match the max level and the gear tiers (11, 18, 35, 45). |
| `mobs.spawn-distance` | `32` | The mob appears next to the block you look at, up to this many blocks away. Looking further or at the sky spawns it at your feet. 1 to 128. |
| `mobs.clear-radii` | `[16, 32, 64, 128]` | Buttons on "Clear hostile mobs". At most 7. Blocks. 1 to 512; very large radii only reach loaded chunks. |
| `mobs.clear-keeps-named` | `true` | `true`: clearing skips mobs that have a custom name **and** are persistent, which is what a name tag does. `false`: removes them too. |
| `mobs.pause-reasons` | `[NATURAL, REINFORCEMENTS, PATROL]` | Spawn reasons cancelled while spawning is paused. Names of Paper's `CreatureSpawnEvent.SpawnReason`; an unknown name logs a warning. |

`NATURAL` is normal spawning in the dark (almost every cave mob), `REINFORCEMENTS` is zombies calling help (DwellerCore raises that chance with level) and `PATROL` is pillager patrols. Others you can add: `SPAWNER`, `TRIAL_SPAWNER`, `SPAWNER_EGG`, `JOCKEY`, `MOUNT`, `RAID`, `VILLAGE_INVASION`, `SLIME_SPLIT`, `SILVERFISH_BLOCK`, `BUILD_IRONGOLEM`. Mobs spawned from the panel use the reason `CUSTOM` and are never paused. Pausing does not stop spawners or spawn eggs unless you add their reasons.

## server {#server-config}

| Key | Default | Effect |
|---|---|---|
| `server.restart.countdowns` | `[60, 300, 600, 1800]` | Seconds offered on the Restart button, one clock each, at most 7. Entries of 0 or less are skipped. Safe range 10 to 86400. |
| `server.restart.warn-at` | `[1800, 600, 300, 60, 30, 10, 5, 4, 3, 2, 1]` | Seconds left at which everyone gets the warning (chat and title). The chosen countdown itself is always announced at the start. Values larger than the countdown are ignored. Safe range 1 to 86400. |
| `server.maintenance.kick-online` | `true` | `true`: turning maintenance on kicks every online player without `dwelleradmin.use` at once. `false`: they stay until they leave. New logins are refused either way. |
| `server.vanish.hide-join-quit-messages` | `true` | `true`: a vanished admin's join and quit messages are not shown. `false`: they show as normal. |

## messages

MiniMessage text. An empty string turns that message off. Placeholders: `<time>` (like `5 minutes` or `1 hour 30 minutes`) and `<message>`.

| Key | Default | Sent when |
|---|---|---|
| `messages.frozen` | `<aqua>You have been frozen by an admin. You cannot move.` | An admin freezes a player, and again whenever a frozen player joins. |
| `messages.unfrozen` | `<green>You are no longer frozen.` | An admin unfreezes a player. |
| `messages.restart-warning` | `<red>Server restarting in <yellow><time></yellow>.` | Chat line at every warning time. |
| `messages.restart-title` | `<red>Restart in <time>` | Title shown with each warning. |
| `messages.restart-cancelled` | `<green>The server restart was cancelled.` | Broadcast when an admin cancels a countdown. |
| `messages.restart-kick` | `<yellow>The server is restarting. Please rejoin in a minute.` | Kick screen when the countdown reaches 0. Empty: the plain line "The server is restarting." |
| `messages.broadcast` | `<gold><bold>[Server]</bold> <white><message>` | Format of an admin broadcast. `<message>` is the admin's text, MiniMessage allowed. Empty means the Broadcast button sends nothing. |
| `messages.maintenance-kick` | `<yellow>The server is under maintenance. Please try again later.` | Kick of online players, and the refusal screen at login. Empty: both use the plain line "The server is under maintenance." |

## The panel

### Main menu

Items, Depth & world, Mobs, Players and Server. Every sub-menu has a Back button bottom left. Long lists are paged, 45 entries a page, with Previous and Next in the bottom row.

Some buttons ask you to type in chat (the broadcast text). Your next chat line is taken as the answer and never reaches other players. Type `cancel` to stop. The prompt is dropped if you quit.

### Items

Lists every item the installed plugins offer through the `adminItems` hook, grouped as `<Plugin> › <category>` (for example `DwellerTools › Tools`). A new plugin with the hook shows up on its own.

| Click | Gives |
|---|---|
| Left | 1 item to you |
| Shift | A full stack (the item's max stack size) to you |
| Right | Opens a list of online players; click one to give them 1 |

Whatever does not fit in the inventory drops at the receiver's feet. Items are fresh copies with their own names and lore.

| Plugin | Items |
|---|---|
| DwellerTools | Grappling Hook, Drill, Hammer, Paxel. A tool switched off with `enabled: false` is left out. |
| DwellerEnchants | One page per rarity (named as the rarity is configured) with an attuned book for every enabled enchantment at every level, rolled with the **top** of that rarity's chance range, plus an `Unattuned` page with one unattuned book per rarity. |
| DwellerSurface | The four suit pieces (boots first, helmet last) and the daily item, each with full wear time. |

If no plugin offers items, the panel says so in chat and does not open.

### Depth & world {#depth-world}

| Button | What it does |
|---|---|
| Teleport to depth | One stop per `world.depth-step` blocks from Y 0 down, then the bottom of the world (bedrock icon). Each shows its depth. Takes you to your own X/Z (the world spawn's X/Z when you are in another world), then finds a [safe spot](#world). The Y 0 stop ends at `max-teleport-y`. |
| Nearest structure | Every registered structure: custom ones (gold) first, then vanilla (white). Click to search and teleport to the nearest. It aims at the middle of the structure's first piece, one block above that piece's lowest point, then finds a safe spot. If no generated structure is found at the located spot, it uses the located X/Z and your current Y. |
| Surface portal | Only shown when DwellerSurface has the underground portal set. Teleports to its arrival point (or the portal's centre). |
| World spawn | The spawn of the cave world. |
| Your respawn point | Your bed or anchor. A red message when you have none. |
| Info here | World, X/Y/Z, biome and light at your feet, plus every plugin's `adminInfo` lines (DwellerCore: depth, mob level with its spread, elite chance, ore multiplier). Clicking prints them to chat. |

### Mobs {#mobs-page}

| Button | What it does |
|---|---|
| Spawn mob | Pick a type, then a level. Row 2 spawns a normal mob, row 3 an elite, one button per `mobs.levels` entry. The screen stays open for repeated spawns. |
| Clear hostile mobs | One button per `mobs.clear-radii`. Removes every hostile mob (anything that counts as an enemy, bosses and wardens included) in a cube around you, in your world, without drops. Only loaded chunks are reached. |
| Mob spawning | Toggles the pause for the reasons in `mobs.pause-reasons`. The state survives restarts. |

Spawned mobs go through DwellerCore's `adminSpawn`: exact level, no level spread, stats, gear and name like a natural spawn at that level, in any world. Only hostile mobs that are not in DwellerCore's `mobs.no-scale` list get a level at all. The elite flag is the admin's choice, but an elite can still end up with no traits when `elites.trait-count` gives 0 at that level. Without DwellerCore the panel spawns a plain mob and tells you that level and elite were ignored.

### Players

A paged list of online players (head, health, food, gamemode, world, XYZ, and Frozen or Vanished tags). Click one for their screen:

| Button | What it does |
|---|---|
| Teleport to them | You go to their position, in any world. |
| Bring them to you | They come to your position. |
| Open inventory, Open ender chest | The live inventory or ender chest, **editable**. Changes apply at once. |
| Heal | Full health (their max health attribute), fire and freeze cleared. |
| Feed | Food 20, saturation 20. |
| Frozen | Toggle. A frozen player can look around but not change position (in the air they stay in the air). Ender pearls and chorus fruit are blocked; teleports by the panel or other plugins still work. Damage, chat and interaction are not blocked. Saved in `data.yml`, so it survives logout and restart, and the player gets the message again on join. |
| Survival, Creative, Adventure, Spectator | Sets the gamemode. |
| Reset surface suit | Only with DwellerSurface. Sets the damage of every suit piece the player wears or carries to 0 and says how many were reset. A piece keeps the maximum wear time it was made with, so changing [`suit.wear-seconds`](config-surface.md#suit) later does not change old pieces. |
| Daily item | Only with DwellerSurface. Gives the daily item, whatever the player's daily timer says. |

Only online players are listed.

### Server {#server-page}

| Button | What it does |
|---|---|
| Restart | Pick a countdown from `server.restart.countdowns`. Warnings go to chat and as a title at the start and at every `warn-at` time. At 0 everyone (you too) is kicked with `restart-kick`, then one second later the server calls `restart()`. While a countdown runs this button becomes Cancel restart, which broadcasts `restart-cancelled`. A running countdown is lost when the server stops. |
| Broadcast | Asks for a line in chat and sends it with `messages.broadcast`. |
| Maintenance | Toggle. While on, only ops can join. Turning it on kicks online players without `dwelleradmin.use` when `kick-online` is `true`. Saved in `data.yml`, so it stays on across restarts until you turn it off. |
| Stats | TPS for 1, 5 and 15 minutes (shown capped at 20), MSPT, memory used out of max, and players, chunks and entities for each world. Click to refresh. |
| Vanish | Toggle for yourself. Players without `dwelleradmin.use` no longer see you in the world or the player list. Join and quit messages are hidden when `hide-join-quit-messages` is `true`. Saved in `data.yml` and reapplied on every join. |

Vanish only uses Bukkit's player hiding. It does not stop mobs targeting you, does not hide you from `/list` or the commands of other plugins, and does not change chat, death or advancement messages.

## Known limits

- **Maintenance:** only ops get in at login, see the [warning above](#command-and-permission).
- **Restart:** without a restart script (`restart-script` in `spigot.yml`) or a host that starts the server again, `restart()` just stops the server. Set one up before using the button.
- **Nearest structure:** the search runs on the main thread. Radii over about 200 chunks can freeze the server for seconds, rare structures worst. Lower `world.structure-search-radius` on a busy server.
- **Clear:** a cube, not a sphere, only in loaded chunks, only in your world. It removes bosses and wardens too.
- **Pause:** spawners, trial spawners and spawn eggs keep working unless their reasons are in `mobs.pause-reasons`.
- **Name tags:** DwellerCore gives every levelled mob a custom name, so the clear keeps only mobs that also have persistence set (a name tag does that). Mobs that are only named, with no persistence, are removed.
- **Cave world detection:** DwellerAdmin recognises DwellerCore's generator by its class name (`DwellerGenerator`) because it has no compile link. If that class is renamed, the panel falls back to the first world on the server.

## data.yml

`plugins/DwellerAdmin/data.yml`. Written at once on every change and when the plugin disables. Edit it only while the server is stopped.

| Key | Type | Meaning |
|---|---|---|
| `maintenance` | boolean | Maintenance is on. |
| `mobs.paused` | boolean | Mob spawning is paused. |
| `frozen` | list of player UUIDs | Frozen players. |
| `vanished` | list of player UUIDs | Vanished admins. |

Back it up with the rest of `plugins/`. Deleting it clears all four states.

## Hooks

DwellerAdmin finds public methods by name on each plugin's main class by reflection. A plugin without a method is skipped, and a method that throws is logged as `[DwellerAdmin] <plugin>.<method> failed` and treated as absent. Only Bukkit and JDK types appear in the signatures. The contract is in `DwellerAdmin/HOOKS.md`.

| Plugin | Method | Used for |
|---|---|---|
| any | `adminItems()` | Items page. Keys are `Category/id`, in display order. |
| any | `adminInfo(Location)` | Info here lines (MiniMessage). |
| DwellerCore | `adminInfo`, `adminSpawn(Location, EntityType, int, boolean)` | Depth, level, elite chance and ore lines; leveled and elite spawns. |
| DwellerTools | `adminItems` | Grappling Hook, Drill, Hammer, Paxel. |
| DwellerEnchants | `adminItems` | Attuned and unattuned books. |
| DwellerSurface | `adminItems`, `adminPortal()`, `adminResetSuit(Player)` | Suit pieces and daily item, the portal button, the suit reset. |

## Interactions

- **[DwellerCore](config-core.md):** `adminSpawn` and `adminInfo` above. The "cave world" is DwellerCore's world. Teleports stop at `max-teleport-y` because of the roof band (see [fixed values](config-core.md#fixed-values-not-settings)).
- **[DwellerSurface](config-surface.md):** the portal button, the suit reset and the daily item. The surface world is not a cave world, so the depth stops and the spawn button use the cave world even when you stand on the surface.
- **[DwellerTools](config-tools.md), [DwellerEnchants](config-enchants.md):** item sources. A tool turned off in its config is not offered.
- **[LuckPerms](plugins/luckperms.md):** `dwelleradmin.use` can be given there, except for the maintenance login (see the warning above).
- **[InvSee++](plugins/invseeplusplus.md):** overlaps with "Open inventory". The panel needs no extra plugin.
- **Restarts:** DwellerAdmin has nothing to reload. Its Restart button is only a countdown and a call to `restart()`.

## Common problems

| Symptom | Cause | Fix |
|---|---|---|
| `/admin` is unknown or does nothing | No `dwelleradmin.use`, or the plugin failed to load | Check the permission and the startup log. |
| Items page says no plugin offers items | DwellerTools, DwellerEnchants and DwellerSurface are missing or disabled | Install at least one. |
| Spawned mob has no level and the message says a plain mob | DwellerCore is missing, or its `adminSpawn` failed (see the log) | Install DwellerCore or fix the log error. |
| Spawned elite has no traits | `elites.trait-count` gives 0 at that level | Pick a higher level, or change the table in [DwellerCore](config-core.md#elites). |
| Teleport says no safe spot | The area is all rock, lava or liquid within `vertical` and `radius` | Raise them, or teleport somewhere else first. |
| Y 0 stop puts you in a cave under the roof | Intended: `max-teleport-y` is `-17` | Leave it. |
| Server freezes for seconds on Nearest structure | Large `structure-search-radius` | Lower it. |
| Admin refused at login during maintenance | The admin has the permission but is not op | Make them op, or turn maintenance off. |
| Restart ends with the server staying down | No restart script or auto-restart | Set `restart-script` in `spigot.yml` or use the host's auto-restart. |
| Pause is on but mobs still spawn | They come from spawners or eggs, or the reason is not listed | Add the reason to `mobs.pause-reasons` and restart. |
| Log: `mobs.spawn-types: not a spawnable mob` | Wrong id, or not a living mob | Fix the id. |
| Log: `mobs.pause-reasons: unknown spawn reason` | Typo | Fix the name. |
| A message never shows | Its key is empty | Set text. |

## Links

- [Paper documentation](https://docs.papermc.io/)
- [Paper `CreatureSpawnEvent.SpawnReason`](https://jd.papermc.io/paper/org/bukkit/event/entity/CreatureSpawnEvent.SpawnReason.html)
- [MiniMessage format](https://docs.advntr.dev/minimessage/format.html)
- [Spigot configuration (`restart-script`)](https://www.spigotmc.org/wiki/spigot-configuration/)
- [Install](install.md)

# Citizens

**Version 2.0.44-SNAPSHOT (build 4258).** Creates NPCs: stationary or walking characters that can be a fake player (with skin), any mob, or other entities, and can talk, run commands, follow, fight, and hold shops. Player-facing summary: [NPCs](https://craftyplayz.github.io/DwellerMC-wiki/plugins/npcs/).

At startup the log shows `Loaded 2 templates` and `Loaded 0 NPCs`; Citizens downloads its extra libraries (Adventure, MiniMessage, ASM) on first run, so the server needs outbound internet once.

## Basics

- `/npc create <name>` makes a **player-type** NPC at your feet (default type). `--type <ENTITY_TYPE>` makes a mob or other entity NPC.
- Most NPC settings are **traits**: `/npc <trait> ...` (for example `/npc skin`, `/npc lookclose`, `/npc text`, `/npc command`, `/npc equip`, `/npc path`, `/npc moveto`, `/npc owner`). `/trait` adds or removes traits; `/npc select` picks the NPC you target (right-click with the selection item, default `stick`).
- Other commands: `/citizens` (admin: reload, save, help), `/template` (alias `/tpl`; apply saved setups), `/waypoints` (alias `/wp`).
- NPCs are saved to `plugins/Citizens/saves.yml` (`storage.file`), every hour (`storage.save-task.delay: 1hr`) and on shutdown.

## Permissions

Everything is `default: op`. Except where noted there are no defaults for players.

| Permission | Effect |
|---|---|
| `citizens.*`, `citizens.admin.*` | Everything / all admin nodes. |
| `citizens.admin` | Admin commands. |
| `citizens.admin.avoid` | Exempts the player from NPC avoid behavior. |
| `citizens.admin.remove.all` | `/npc remove all`. |
| `citizens.npc.create` | Create NPCs. |
| `citizens.npc.create.<entitytype>` | Create an NPC of that type (one node per type, for example `citizens.npc.create.zombie`). |
| `citizens.npc.<command>` | One node per `/npc` subcommand/trait (`select`, `skin`, `text`, `command`, `tp`, `spawn`, `despawn`, `owner`, `vulnerable`, `equip`, ... about 150 nodes). |
| `citizens.npc.limit.1` to `.10` | Max NPC count a player may own (default false; the default limit in config is 10 owned NPCs). |
| `citizens.templates.*`, `citizens.waypoints.*` | Templates and waypoints. |
| `citizens.npc.edit.*` | Editors (equipment, copier, etc.). |

**LuckPerms:** players need no Citizens permissions to talk to or click NPCs. Give your builder/staff group only the nodes it needs; avoid giving `citizens.admin` or `citizens.*` to anyone who is not trusted, because NPCs can run console commands (`/npc command add ... -o`).

## Config (`plugins/Citizens/config.yml`)

Citizens writes this file on first start; there is no bundled copy. Defaults below are the plugin's built-in values.

### General and NPC behavior

| Key | Default | Effect |
|---|---|---|
| `general.authlib.profile-url` | `https://sessionserver.mojang.com/session/minecraft/profile/` | Skin lookup. |
| `general.debug-mode`, `general.debug-file` | false, empty | Debugging. |
| `general.translation.locale` | system language | Message language. |
| `general.reload-warning-enabled` | true | Warn on `/reload`. |
| `general.reset-formatting-on-color-change` | false | Old color-code behavior. |
| `general.entity-spawn-wait-ticks` | 1s | Wait for chunk load before spawning. Raise on slow disks. |
| `general.resource-pack-path` | `plugins/Citizens/resourcepack` | Save path. |
| `general.color-scheme.message / message-highlight / message-error` | `<green>`, `<yellow>`, `<red>` | Command output colors. |
| `storage.file` | `saves.yml` | NPC save file. |
| `storage.save-task.delay` | 1hr | Autosave interval. |
| `npc.limits.default-limit` | 10 | NPCs per player (permission `citizens.npc.limit.N` raises it). |
| `npc.limits.max-permission-checks` | 100 | How many limit nodes are checked. |
| `npc.defaults.npc-cost` | 100.0 | Vault cost to create. Not used without Vault. |
| `npc.server-ownership` | false | Server owns NPCs, not the creator. |
| `npc.selection.item` / `npc.selection.message` | `stick` / `Selected [[<npc>]] (ID [[<id>]]).` | Select tool. |
| `npc.chunks.always-keep-loaded` | false | Keep NPC chunks loaded. Leave false in a 2000-block-deep world. |
| `npc.default.spawn-invincibility-duration` | 1s | Invulnerable time after spawn. |
| `npc.default.reset-yaw-on-spawn` | true | Fix facing on spawn. |
| `npc.default.look-close.enabled` / `range` / `random-look-enabled` / `random-look-delay` | false / 10 / false / 3s | Look at nearby players. |
| `npc.default.look-close.realistic-looking` / `disable-while-navigating` | false / true | Line-of-sight check / stop looking while walking. |
| `npc.default.talk-close.enabled` / `range` / `text` | false / 5 / `Hi, I'm <npc>!` | Auto talk. |
| `npc.default.bossbar-view-range` | 64 | Range of the NPC boss bar trait. |
| `npc.defaults.enable-scoreboard-teams` | true | NPC joins a scoreboard team (used for name tag control). |
| `npc.tablist.disable` | true | Remove player NPCs from the tab list. |
| `npc.tablist.remove-packet-delay` | 2t | Delay of that removal. |
| `npc.player.remove-from-list` | true | Remove player NPCs from the server's player list (`getOnlinePlayers`). |
| `npc.always-use-name-holograms` | false | Names as holograms. |
| `npc.use-packet-holograms` | false | Experimental packet holograms. |
| `npc.hologram.default-renderer-settings`, `default-line-height` (0.4), `default-view-range`, `default-vertical-view-range` (-1), `update-rate` (1s), `always-update-position` (false) | | Hologram tuning. |
| `npc.skins.max-retries` | -1 | Skin retries (-1 = unlimited). |
| `npc.skins.retry-delay` | 5s | Between retries. |
| `npc.skins.view-distance` | 100 | Skin packet range. |
| `npc.skins.player-join-update-delay` | 1s | Send skins after join. |
| `npc.skins.placeholder-update-frequency` | 5m | Placeholder skins. |
| `npc.skins.use-latest-by-default` | false | Refresh skins periodically. |
| `npc.skins.rotation-update-degrees` | 90 | Head packet threshold. |
| `npc.packets.update-delay` | 30 | Packet refresh. |
| `npc.follow.teleport-across-worlds` | false | Follow across worlds. |
| `npc.text.talk-item` | `*` | Item needed to talk (`*` = any). |
| `npc.text.default-random-text-delay-min/max` | 5s / 10s | Random chat delay. |
| `npc.text.speech-bubble-duration` | 50t | Speech bubble time. |
| `npc.chat.format.*` | `[<npc>]: <text>`, `<npc>: <text>`, `[<npc>] -> [<target>]: <text>` | NPC chat lines. |
| `npc.chat.options.range` / `bystanders-hear-targeted-chat` / `max-number-of-targets-to-show` / `talk-to-npcs` | 5 / false / 2 / true | Chat reach. |
| `npc.commands.global-cooldown` | 1s | Shared command cooldown. |
| `npc.commands.error-messages.*` | English strings | Messages for command costs, cooldowns, permissions. |
| `npc.shops.default-item`, `npc.shops.add-default-item-description`, `npc.shops.global-view-permission` | defaults / false / empty | NPC shops. |
| `npc.controllable.*` | ground modifier 1.0, max ground speed 0.5, max flying speed 0.75, boat controls true | Rideable NPCs. |
| `npc.movement.water-speed-modifier` | 1.15 | Swim speed. |

### Pathfinding

| Key | Default | Effect |
|---|---|---|
| `npc.pathfinding.pathfinder-type` | `MINECRAFT` | `MINECRAFT`, `CITIZENS` or `CITIZENS_ASYNC`. |
| `npc.pathfinding.minecraft.maximum-search-blocks` | 1024 | Search limit for the vanilla pathfinder. |
| `npc.pathfinding.minecraft.disable-fallback-navigation` | false | Do not accept near-enough targets. |
| `npc.pathfinding.citizens.blocks-per-tick` | 250 | Work per tick. |
| `npc.pathfinding.citizens.maximum-search-blocks` | 1024 | Search limit. |
| `npc.pathfinding.citizens.check-bounding-boxes` / `experimental-jumps` / `open-doors` | false / false / false | Extra checks. |
| `npc.pathfinding.default-range-blocks` | 100.0 | Range of one path. |
| `npc.pathfinding.update-path-rate` | 1s | Repath rate for moving targets. |
| `npc.pathfinding.attack-range` | 1.75 | Melee reach. |
| `npc.pathfinding.default-stuck-action` | `none` | Or `teleport to destination`. |
| `npc.pathfinding.allowed-fall-distance` | -1 | -1 uses the default. |
| `npc.pathfinding.debug` | false | Show debug blocks. |

## Data files

`plugins/Citizens/`: `config.yml`, `saves.yml` (all NPCs), `templates.yml`, `lang/`, `skins/`, `resourcepack/`. Back up `saves.yml`.

## Interactions with DwellerMC

**Mob-type NPCs and DwellerCore levels.** DwellerCore levels every hostile mob that spawns in its world, but it skips entities that Citizens marks as NPCs (the `NPC` metadata). Mob NPCs keep their own name, stats and equipment, never get a `Lv N` tag and can't become elites. Player-type and passive NPCs are never leveled anyway.

If a hostile mob NPC still shows a `Lv N` name, Citizens marked it after it spawned. Remove and recreate it, and report it so DwellerCore can be changed.

**Other interactions**

- **TAB:** Citizens removes player NPCs from the tab list after 2 ticks (`npc.tablist.disable`). With TAB's `scoreboard-teams` active, NPCs also use scoreboard teams (`enable-scoreboard-teams`). If an NPC name tag shows the wrong prefix or colour, set `npc.defaults.enable-scoreboard-teams: false`. TAB's `belowname-objective` (off by default) shows `NPC` for NPC entries.
- **LibsDisguises / packetevents:** Citizens soft-depends on packetevents and has a `/npc disguise` trait that uses LibsDisguises. A disguised NPC needs both loaded (they are).
- **BlockDisguise:** works on players only, not NPCs.
- **Plan:** NPC players are not counted as real sessions.
- **Chat:** NPC lines use `npc.chat.format.*`; they are not routed through EssentialsChat or DiscordSRV.
- **Boss bar / depth HUD:** an NPC with a boss bar trait uses Adventure boss bars like DwellerCore elites; both show in the same top-of-screen stack.
- **Chunks:** NPCs only exist while their chunk is loaded. In a world 2000 blocks deep, NPCs far from players are unloaded, which is normal. `npc.chunks.always-keep-loaded: true` would hold chunks open; avoid it.
- **WorldGuard:** an NPC's damage is controlled by `/npc vulnerable`; WorldGuard region flags can still apply to mob-type NPCs.

## Recommended settings

- Keep `npc.limits.default-limit` small and give only staff `citizens.npc.create`.
- Keep `npc.tablist.disable: true` and `npc.player.remove-from-list: true` (defaults) so NPCs do not appear as players.
- Set `npc.pathfinding.pathfinder-type: MINECRAFT` unless NPCs must walk complex paths.
- Use `/npc skin <name>` rather than fetching skins from every NPC name (`npc.skins.use-latest-by-default: false`).

## Problems

| Symptom | Cause / fix |
|---|---|
| Plugin loads but NPCs do not appear | Wait for the chunk, or `/npc spawn`; check the NPC is not despawned. |
| NPC skin stays Steve | Mojang rate limit; Citizens retries. Use `/npc skin <name>`. |
| Hostile mob NPC shows `Lv N` name | See [Interactions with DwellerMC](#interactions-with-dwellermc). |
| NPC runs commands for players | Check `/npc command` entries; staff-only. |
| Citizens complains about Minecraft version | `advanced.check-minecraft-version` is for compatibility; update Citizens to a build for 26.x if it disables itself. |

See also [TAB](tab.md), [Disguises](disguises.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [Citizens wiki](https://wiki.citizensnpcs.co/)
- [Citizens commands](https://wiki.citizensnpcs.co/Commands)
- [Citizens permissions](https://wiki.citizensnpcs.co/Permissions)

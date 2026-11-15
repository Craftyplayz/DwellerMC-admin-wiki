# WorldGuard

| | |
|---|---|
| Version | 7.0.20 (build 2410-0b6c3f1) |
| Jar | `worldguard-bukkit-7.0.20.jar` |
| Depends | WorldEdit (required, installed) |
| API version | 26.3 |
| Data folder | `plugins/WorldGuard/` |
| Folia | supported |

WorldGuard protects areas ("regions") and changes world rules. Regions are boxes with flags such as `build deny` or `mob-spawning deny`. It also has a blacklist and many world-wide switches.

!!! note "Nobody can use WorldGuard until you give permissions"
    WorldGuard registers its permission nodes in code, not in `plugin.yml`. Ops can use everything (`op-permissions: true`). Players have nothing. Give nodes with [LuckPerms](luckperms.md).

## Regions on DwellerMC

The world runs from Y -2032 to Y 15. A region has a minimum and maximum Y. If you define a region with a normal selection, it only covers the selected Y range.

- Use `//expand vert` after selecting two points. It stretches the selection from the world's bottom (-2032) to its top (15). Then `/rg define <id>`.
- Check with `/rg info <id>`: the output shows the min and max point.
- Regions defined with the `-g` flag are templates and cover nothing.
- A region protecting only the spawn area should still use `//expand vert`, otherwise someone can dig in from below.
- The hostile-mob level system is untouched by regions. A `mob-spawning deny` or `deny-spawn` flag cancels the spawn before DwellerCore can level it, so the mob never exists.
- Falling into the void does not exist here (bedrock floor), so the `player-damage.*void*` options do nothing useful.

Region ids are case-insensitive and may use letters, digits, `_`, `-`, `,`, `'`, `/`. `__global__` is a built-in region covering the whole world. Flags set on `__global__` apply everywhere, with the lowest priority.

## Commands

`/region` and `/rg` are the same. Console needs `-w <world>`.

### Create and remove

| Command | Does | Permission |
|---|---|---|
| `/rg define [-g] <id> [owners...]` | Create from your WorldEdit selection. | `worldguard.region.define` |
| `/rg redefine [-g] <id>` (`update`, `move`) | Replace the area with your selection. | `worldguard.region.redefine.<own\|member\|id>` |
| `/rg remove [-f\|-u] <id>` | Delete. With child regions give `-u` (unparent) or `-f` (delete children). | `worldguard.region.remove.*` |
| `/rg claim <id>` | Self-service claim. | `worldguard.region.claim` |

### Members and owners

Prefix with `g:` for a permission group (for example `g:mod`).

| Command | Permission |
|---|---|
| `/rg addowner <id> <names...>` (`ao`) | `worldguard.region.addowner.*` |
| `/rg addmember <id> <names...>` (`am`) | `worldguard.region.addmember.*` |
| `/rg removeowner [-a] <id> <names...>` (`ro`) | `worldguard.region.removeowner.*` |
| `/rg removemember [-a] <id> <names...>` (`rm`) | `worldguard.region.removemember.*` |

`-a` removes everyone.

### Info and flags

| Command | Does | Permission |
|---|---|---|
| `/rg info [-u] [-s] [id]` | Details. Standing in a region without an id shows the region here. `-s` selects it. | `worldguard.region.info.*` |
| `/rg list [-i <text>] [-p <player>] [-s] [page]` | List regions. | `worldguard.region.list` (or `.list.own`) |
| `/rg select [id]` (`sel`) | Select a region in WorldEdit. | `worldguard.region.select.*` |
| `/rg flags <id> [-p page]` | Clickable flag list. | `worldguard.region.info.*` |
| `/rg flag <id> <flag> [-g <group>] [-e] [value]` (`f`) | Set a flag. No value removes it. `-e` sets an empty value. | `worldguard.region.flag.regions.<scope>.*` and `worldguard.region.flag.flags.<flag>.<value>.<scope>` |
| `/rg setpriority <id> <number>` | Priority, default 0. Higher wins where regions overlap. | `worldguard.region.setpriority.*` |
| `/rg setparent <id> [parent]` | Inherit members, owners and flags from a parent. | `worldguard.region.setparent.*` |
| `/rg teleport [-c] [-s] <id>` (`tp`) | Go to the `teleport` flag location, or `spawn` with `-s`. `-c` goes to the centre (spectator mode only). | `worldguard.region.teleport.*` |

### Management

| Command | Does | Permission |
|---|---|---|
| `/rg load` / `/rg save` | Reload or write region data. Loading drops unsaved changes. | `worldguard.region.load` / `.save` |
| `/rg migratedb <from> <to>` | `yaml` or `mysql`. Blocks the server. Back up first. | `worldguard.region.migratedb` |
| `/rg migrateuuid` | Names to UUIDs. | `worldguard.region.migrateuuid` |
| `/rg migrateheights` | Extend old regions to new world height. | `worldguard.region.migrateheights` |
| `/rg bypass` | Toggle your region bypass (`regions.disable-bypass-by-default` must be on for this to matter). | `worldguard.region.toggle-bypass` |

### Other commands

| Command | Does | Permission |
|---|---|---|
| `/wg version` | Version | none |
| `/wg reload` | Reload config, blacklist and regions | `worldguard.reload` |
| `/wg report [-p]` | Writes `plugins/WorldGuard/report.txt`, `-p` uploads it | `worldguard.report` (`.pastebin` for upload) |
| `/wg profile [-p] [-i ms] [-t thread] [minutes]` / `/wg stopprofile` | Simple CPU profiler | `worldguard.profile` |
| `/wg running` | Running tasks | `worldguard.running` |
| `/wg flushstates` | Flush internal player states | `worldguard.flushstates` |
| `/wg debug test*` | Simulate events for a player | `worldguard.debug.event` |
| `/god [-s] [player]`, `/ungod` | Invincibility | `worldguard.god`, `.god.other` |
| `/heal [-s] [player]` | Heal | `worldguard.heal`, `.heal.other` |
| `/slay [-s] [player]` | Kill | `worldguard.slay`, `.slay.other` |
| `/locate [player]` | Point compass | `worldguard.locate` |
| `/stack` (`/;`) | Stack inventory items | `worldguard.stack` (`.illegitimate` for oversize stacks) |
| `/stopfire [world]`, `/allowfire [world]` | Stop or allow fire spread | `worldguard.fire-toggle.stop` |
| `/stoplag [-c] [-i] [-s]` (`/halt-activity`) | Remove all entities in all worlds and freeze physics. Also deletes entities in chunks as they load. | `worldguard.halt-activity` |

!!! warning "/stoplag and /slay"
    `/stoplag` removes every entity including leveled mobs and elites. Use `-c` to switch it off again. `/heal` and `/god` clash with the same commands in EssentialsX; see below.

## Permissions

By default no player holds any of these. Ops hold all of them while `op-permissions` is true.

| Node | Effect |
|---|---|
| `worldguard.region.bypass.<world>` | Ignore region protection in that world. Use `worldguard.region.bypass.world`. |
| `worldguard.notify` | See notices from blacklist `notify` actions and `notify-enter`/`notify-leave` flags. |
| `worldguard.chest-protection.override` | Open protected chests. |
| `worldguard.god.override-regions` | Stay invincible where a region denies invincibility. |
| `worldguard.auto-invincible` | Auto god mode on join (needs `auto-invincible-permission` on). |
| `worldguard.override.potions`, `.lighter`, `.infinite-stack` | Bypass the matching config limits. |
| `worldguard.region.wand` | Use the region wand item. |
| `worldguard.region.unlimited` | Ignore claim limits. |
| `worldguard.region.<command>.<scope>` | `scope` is `own`, `member`, or a region id. `.*` is allowed. Example: `worldguard.region.addmember.own.*`. |
| `worldguard.region.locationoverride.*` | Bypass `regions.location-flags-only-inside-regions`. |

A good moderator set: `worldguard.region.info.*`, `worldguard.region.list`, `worldguard.region.select.*`, `worldguard.notify`, `worldguard.region.teleport.*`. A good admin set: `worldguard.region.define`, `worldguard.region.redefine.*`, `worldguard.region.remove.*`, `worldguard.region.flag.regions.*`, `worldguard.region.flag.flags.*`, `worldguard.region.addowner.*`, `worldguard.region.addmember.*`, `worldguard.region.removeowner.*`, `worldguard.region.removemember.*`, `worldguard.region.setpriority.*`, `worldguard.region.setparent.*`, `worldguard.region.bypass.world`, `worldguard.region.toggle-bypass`, `worldguard.reload`.

## Flags

Set with `/rg flag <id> <flag> <value>`. State flags take `allow` or `deny`.

### Protection

`build` (the master switch), `block-break`, `block-place`, `interact`, `use`, `chest-access`, `damage-animals`, `ride`, `pvp`, `sleep`, `respawn-anchors`, `tnt`, `vehicle-place`, `vehicle-destroy`, `lighter`, `block-trampling`, `frosted-ice-form`, `item-frame-rotation`, `firework-damage`, `use-anvil`, `use-dripleaf`. They default to the `build` result: members yes, non-members no (inside a region that has owners or members).

`passthrough` makes a region not protect anything by itself, used for pure flag areas.

### Mobs, fire, explosions

`creeper-explosion`, `enderdragon-block-damage`, `ghast-fireball`, `other-explosion`, `fire-spread`, `enderman-grief`, `snowman-trails`, `ravager-grief`, `mob-damage`, `mob-spawning`, `entity-painting-destroy`, `entity-item-frame-destroy`, `wither-damage`. Also `deny-spawn` (list of entity types, e.g. `creeper,zombie`).

### Natural events

`lava-fire`, `lightning`, `water-flow`, `lava-flow`, `snow-fall`, `snow-melt`, `ice-form`, `ice-melt`, `frosted-ice-melt`, `mushroom-growth`, `leaf-decay`, `grass-growth`, `mycelium-spread`, `vine-growth`, `rock-growth`, `sculk-growth`, `crop-growth`, `soil-dry`, `coral-fade`, `copper-fade`. All allow by default.

### Movement and messages

`entry`, `exit` (non-members by default), `exit-via-teleport`, `exit-override`, `enderpearl`, `chorus-fruit-teleport`, `notify-enter`, `notify-leave`, `entry-deny-message`, `exit-deny-message`, `greeting`, `greeting-title`, `farewell`, `farewell-title`, `teleport-message`, `teleport` (location), `spawn` (location).

### Gameplay

`item-pickup`, `item-drop`, `exp-drops`, `invincible`, `fall-damage`, `natural-health-regen`, `natural-hunger-drain`, `deny-message`, `time-lock`, `weather-lock`, `game-mode`, `heal-delay`, `heal-amount`, `heal-min-health`, `heal-max-health`, `feed-delay`, `feed-amount`, `feed-min-hunger`, `feed-max-hunger`, `blocked-cmds`, `allowed-cmds`.

### Misc

`pistons`, `send-chat`, `receive-chat`, `potion-splash`.

Flag details and values: see the flags page linked below.

### Examples

```
//pos1
//pos2
//expand vert
/rg define spawn
/rg flag spawn build deny
/rg flag spawn mob-spawning deny
/rg flag spawn greeting You are at spawn
/rg addmember spawn SomePlayer
```

For a quiet spawn, `mob-spawning deny` stops all spawns inside, including spawn eggs and commands. Use `deny-spawn zombie,skeleton` to block only some types.

## Configuration

Two files:

- `plugins/WorldGuard/config.yml`: global settings.
- `plugins/WorldGuard/worlds/<world>/config.yml`: per-world settings. WorldGuard creates it from the default when the world first loads.
- `plugins/WorldGuard/worlds/<world>/blacklist.txt`: blacklist for that world.

Both files start nearly empty and WorldGuard fills them with every option on first load. The defaults below are the code defaults.

### Global `config.yml`

| Key | Default | Effect |
|---|---|---|
| `regions.sql.use` | `false` | Store regions in MySQL instead of YAML. |
| `regions.sql.dsn` | `jdbc:mysql://localhost/worldguard` | MySQL URL. |
| `regions.sql.username` / `password` / `table-prefix` | `worldguard` / `worldguard` / empty | MySQL login. |
| `regions.use-creature-spawn-event` | `true` | Needed for `mob-spawning` and `deny-spawn`. |
| `regions.use-pre-creature-spawn-event` | `false` | Paper pre-spawn event (cheaper). |
| `regions.disable-bypass-by-default` | `false` | If `true`, bypass holders must run `/rg bypass` first. |
| `regions.announce-bypass-status` | `false` | Tell players when bypass is on. |
| `regions.uuid-migration.perform-on-next-start` | `true` | Convert old names to UUIDs. |
| `regions.uuid-migration.keep-names-that-lack-uuids` | `true` | Keep names with no UUID. |
| `auto-invincible-permission` | `false` | Use the permission `worldguard.auto-invincible`. |
| `auto-invincible-group` | `false` | Use the group of that name. |
| `auto-no-drowning-group` | `false` | Same for drowning. |
| `use-player-move-event` | `true` | Needed for `entry`, `exit`, `greeting`, `farewell`, `notify-*`. Turn off to save CPU if unused. |
| `use-player-teleports` | `true` | Region checks on teleport. |
| `use-particle-effects` | `true` | Particle effects on some flags. |
| `disable-permission-cache` | `false` | Do not cache permission checks. |
| `security.deop-everyone-on-join` | `false` | De-op everyone on join. |
| `security.block-in-game-op-command` | `false` | Block `/op` in game. |
| `security.host-keys` | empty | Host keys for connection checks. |
| `security.host-keys-allow-forge-clients` | `false` | |

### Per-world `config.yml`

| Key | Default | Effect |
|---|---|---|
| `summary-on-start` | `true` | Print a summary at start. |
| `op-permissions` | `true` | Ops count as having all WorldGuard permissions. |
| `build-permission-nodes.enable` | `false` | Use `worldguard.build.*` style nodes. |
| `build-permission-nodes.deny-message` | `&eSorry, but you are not permitted to do that here.` | |
| `event-handling.block-entity-spawns-with-untraceable-cause` | `false` | |
| `event-handling.ignore-hopper-item-move-events` | `false` | |
| `event-handling.break-hoppers-on-denied-move` | `true` | |
| `protection.item-durability` | `true` | Items lose durability. |
| `protection.remove-infinite-stacks` | `false` | |
| `protection.disable-xp-orb-drops` | `false` | |
| `protection.use-max-priority-association` | `false` | |
| `gameplay.block-potions-overly-reliably` | `false` | |
| `gameplay.disable-conduit-effects` | `false` | |
| `simulation.sponge.enable` / `radius` / `redstone` | `false` / `3` / `false` | Sponge block simulation. |
| `default.pumpkin-scuba` | `false` | |
| `default.disable-health-regain` | `false` | |
| `physics.no-physics-gravel` / `no-physics-sand` | `false` | |
| `physics.vine-like-rope-ladders` | `false` | |
| `physics.allow-portal-anywhere` | `false` | |
| `physics.disable-water-damage-blocks` | empty | |
| `ignition.block-tnt` / `block-tnt-block-damage` / `block-lighter` | `false` | |
| `fire.disable-lava-fire-spread` / `disable-all-fire-spread` | `false` | |
| `fire.disable-fire-spread-blocks` / `lava-spread-blocks` | empty | |
| `mobs.block-creeper-explosions` / `block-creeper-block-damage` | `false` | |
| `mobs.block-wither-explosions` / `-block-damage` / `block-wither-skull-explosions` / `-block-damage` | `false` | |
| `mobs.block-enderdragon-block-damage` / `block-enderdragon-portal-creation` | `false` | |
| `mobs.block-fireball-explosions` / `-block-damage` | `false` | |
| `mobs.block-windcharge-explosions` | `false` | |
| `mobs.block-other-explosions` | `false` | |
| `mobs.anti-wolf-dumbness` | `false` | |
| `mobs.allow-tamed-spawns` | `true` | |
| `mobs.disable-enderman-griefing` | `false` | |
| `mobs.disable-snowman-trails` | `false` | |
| `mobs.block-painting-destroy` / `block-item-frame-destroy` / `block-armor-stand-destroy` | `false` | |
| `mobs.block-plugin-spawning` | `true` | Region spawn flags also apply to spawns caused by plugins. |
| `mobs.block-above-ground-slimes` | `false` | |
| `mobs.block-zombie-door-destruction` | `false` | |
| `mobs.block-vehicle-entry` | `false` | Mobs cannot enter vehicles. |
| `player-damage.disable-fall-damage` / `lava` / `fire` / `lightning` / `drowning` / `suffocation` / `contact` / `explosion` / `mob` / `death-messages` | `false` | Turn off that damage for everyone. |
| `player-damage.teleport-on-suffocation` | `false` | |
| `player-damage.disable-void-damage` / `teleport-on-void-falling` / `reset-fall-on-void-teleport` | `false` | |
| `chest-protection.enable` | `false` | Sign-based chest protection. |
| `chest-protection.disable-off-check` | `true` | |
| `crops.disable-creature-trampling` / `disable-player-trampling` | `false` | Same pair for `turtle-egg` and `sniffer-egg`. |
| `weather.*` | all `false` | `prevent-lightning-strike-blocks` (list), `disable-lightning-strike-fire`, `disable-thunderstorm`, `disable-weather`, `disable-pig-zombification`, `disable-villager-witchification`, `disable-powered-creepers`, `always-raining`, `always-thundering`. Weather does not exist in a roofed cave. |
| `dynamics.*` | all `false` | `disable-mushroom-spread`, `ice-melting`, `snow-melting`, `snow-formation`, `ice-formation`, `leaf-decay`, `grass-growth`, `mycelium-spread`, `vine-growth`, `rock-growth`, `sculk-growth`, `crop-growth`, `soil-dehydration`, `soil-moisture-change`, `coral-block-fade`, `copper-block-fade`, `lava-harden`. `snow-fall-blocks` is a list. |
| `regions.enable` | `true` | Region protection on or off for this world. |
| `regions.invincibility-removes-mobs` | `false` | Mobs that hit an invincible player are removed. |
| `regions.cancel-chat-without-recipients` | `true` | |
| `regions.nether-portal-protection` | `true` | |
| `regions.titles-always-use-default-times` | `true` | |
| `regions.fake-player-build-override` | `true` | Fake players (other plugins) may build where their owner could. |
| `regions.explosion-flags-block-entity-damage` | `true` | |
| `regions.high-frequency-flags` | `false` | Enable flags that cost CPU (water-flow, lava-flow, fire checks). |
| `regions.protect-against-liquid-flow` | `false` | Stop liquid flowing into regions from outside. Useful in cave worlds. |
| `regions.wand` | `minecraft:leather` | Region wand item. |
| `regions.max-claim-volume` | `30000` | Max blocks for `/rg claim`. |
| `regions.claim-only-inside-existing-regions` | `false` | |
| `regions.set-parent-on-claim` | empty | Parent for claimed regions. |
| `regions.location-flags-only-inside-regions` | `false` | |
| `regions.max-region-count-per-player.default` | `7` | Claim limit per player. |
| `regions.use-paper-entity-origin` | `false` | |
| `iconomy.enable` / `buy-on-claim` / `buy-on-claim-price` | `false` / `false` / `1.0` | Needs an economy plugin. None installed. |
| `blacklist.use-as-whitelist` | `false` | |
| `blacklist.logging.console.enable` | `true` | |
| `blacklist.logging.database.*` | disabled | `enable false`, `dsn`, `user root`, `pass`, `table blacklist_events`. |
| `blacklist.logging.file.enable` | `false` | `path worldguard/logs/%Y-%m-%d.log`, `open-files 10`. |

### Blacklist

`blacklist.txt` blocks items and blocks. Syntax: a block of names in `[brackets]`, then events, each with actions:

```
[tnt]
on-place=deny,notify
on-use=deny
```

Events: `on-break`, `on-destroy-with`, `on-place`, `on-use`, `on-interact`, `on-drop`, `on-acquire`, `on-equip`, `on-dispense`. Actions: `deny`, `allow` (whitelist mode), `notify`, `log`, `tell`, `kick`, `ban`.

## Data files

| Path | What |
|---|---|
| `plugins/WorldGuard/config.yml` | Global config |
| `plugins/WorldGuard/worlds/world/config.yml` | Per-world config |
| `plugins/WorldGuard/worlds/world/regions.yml` | Regions of that world (YAML store) |
| `plugins/WorldGuard/worlds/world/blacklist.txt` | Blacklist |
| `plugins/WorldGuard/report.txt` | Output of `/wg report` |

Back up `regions.yml` before `//` work or migrations.

## Interactions

- **WorldEdit:** required. Regions are defined from the WorldEdit selection. WorldEdit edits inside a region are not blocked unless the player lacks the WorldGuard permission (WorldGuard hooks WorldEdit). See [WorldEdit](worldedit.md).
- **LuckPerms:** gives the nodes. See [LuckPerms](luckperms.md).
- **EssentialsX:** `/god`, `/heal` and `/feed` exist in both. Whichever registers first wins, or use `/worldguard:heal`. Teleport commands from Essentials go through WorldGuard checks (`exit`, `entry`).
- **CoreProtect:** independent. Denied actions never reach its log. See [CoreProtect](coreprotect.md).
- **DwellerCore (mob levels):** denied spawns never get a level. Spawn eggs and commands go through the same spawn event, so `mob-spawning deny` blocks them too.
- **Vein miner plugins and similar:** may break blocks without a player event that WorldGuard sees as the player. Test a protected area with them before relying on it.
- **spark:** `/spark profiler` shows WorldGuard cost if `use-player-move-event` is heavy. See [spark](spark.md).

## Recommended for this server

1. Give the owner/admin the nodes above. Do not give `*`.
2. Protect spawn: select with WorldEdit, `//expand vert`, `/rg define spawn`, then flags `build deny` and `mob-spawning deny`.
3. In the per-world config, set `regions.protect-against-liquid-flow: true` if lava flow into protected areas is a worry.
4. Leave `use-player-move-event: true` only if you use greeting, farewell or entry flags.
5. Leave weather and fall damage options alone; they have no use in a cave.

## Common problems

| Problem | Fix |
|---|---|
| Players can dig under a region | The region does not span the full Y range. `/rg info` and redefine with `//expand vert`. |
| "You don't have permission" for `/rg` | Node missing. Check with `/lp verbose record`. |
| Region flag does nothing | A higher-priority region overrides it. `/rg info` at the spot. Check `/rg setpriority`. |
| `/rg define` says no selection | Make a WorldEdit selection first (`//wand`). |
| Greeting never shows | `use-player-move-event` is `false`. |
| `mob-spawning deny` leaves mobs | Existing mobs and some spawn types (`regions.use-creature-spawn-event` off) are not affected. |

## Links

- [WorldGuard docs](https://worldguard.enginehub.org/en/latest/)
- [Region commands](https://worldguard.enginehub.org/en/latest/regions/commands/)
- [Flags](https://worldguard.enginehub.org/en/latest/regions/flags/)
- [Permissions](https://worldguard.enginehub.org/en/latest/permissions/)
- Players see the effect on the [player wiki](https://craftyplayz.github.io/DwellerMC-wiki/).

Related: [Index](../index.md), [WorldEdit](worldedit.md), [LuckPerms](luckperms.md).

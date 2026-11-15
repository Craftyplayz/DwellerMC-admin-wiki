# EssentialsX

EssentialsX 2.22.0 with two add-on modules, **EssentialsXChat** and **EssentialsXSpawn**. It gives players homes, `/tpa`, `/msg`, kits, an economy and a fixed spawn, and gives admins moderation tools.

Other pages: [Commands and permissions](essentials-commands.md), [Config reference](essentials-config.md).

| Jar | Adds |
|---|---|
| `EssentialsX-2.22.0.jar` | Core: commands, homes, warps, kits, economy, AFK, jails, signs |
| `EssentialsXChat-2.22.0.jar` | Chat format, local chat radius, shout and question prefixes, `/toggleshout` |
| `EssentialsXSpawn-2.22.0.jar` | `/spawn`, `/setspawn`, first-join spawn, respawn handling, newbie kit |

EssentialsXProtect and EssentialsXGeoIP are not installed, so the `protect:` section of the config does nothing.

!!! warning "Set the spawn before opening the server"
    Until `/setspawn` is run, Essentials uses the **world spawn**. In this world that is on top of the bedrock roof. Stand in a safe cave and run both:

    ```
    /setspawn
    /setspawn newbies
    ```

    Both must be done inside a cave.

## Install

1. Put the three jars in `plugins/`.
2. Start the server once. `plugins/Essentials/config.yml` is created.
3. Edit the config (see [Recommended setup](#recommended-setup)) and restart. `/essentials reload` re-reads most keys, but the listener priorities need a restart.

Vault is not installed (the `Vaults` plugin is a different plugin). Without Vault, other plugins cannot use the Essentials economy, and the `{PREFIX}`, `{SUFFIX}` and `{GROUP}` chat placeholders may stay empty. Install Vault if you want LuckPerms prefixes in chat.

## First join and respawn

Settings in use: `respawn-listener-priority: high`, `spawn-join-listener-priority: high`, `respawn-at-home: false`, `random-spawn-location: none`, `random-respawn-location: none`, `spawn-on-join: false`, `newbies.spawnpoint: newbies`, `newbies.kit: tools`.

DwellerCore's random cave spawn is off on this server (`spawn.random-cave: false` in DwellerCore's config), so Essentials decides where new players and bed-less deaths go. DwellerCore still protects beds: it notes the bed location before any plugin handles the respawn (priority LOWEST) and puts it back afterwards (HIGHEST).

### First join

1. EssentialsXSpawn teleports the player to the spawn named `newbies`, one tick after join. If `newbies` was never set it uses the `default` spawn. If neither is set it uses the world spawn (the roof).
2. Two ticks after join the player gets the welcome broadcast (`newbies.announce-format`) and the `newbies.kit` kit. The kit ignores permissions, cost and cooldown.

Players who have joined before are not moved on join, because `spawn-on-join` is `false`.

### Death

With `respawn-at-home: false`, EssentialsXSpawn sets the respawn point to the `/setspawn` spawn for the player's group (`default` if the group has none), even when the player has a valid bed. DwellerCore then restores the bed, so the result is: bed if there is one, otherwise the `/setspawn` spawn.

| Situation | Where the player respawns |
|---|---|
| Has a bed | Their bed (restored by DwellerCore) |
| No bed | The `/setspawn` spawn |
| Respawn anchor | The `/setspawn` spawn (anchors do not work in this world anyway) |
| Jailed | Essentials does not move them |
| `/setspawn` never run | The world spawn (on the roof) |

Keep `respawn-at-home: false`. Setting it to `true` would send bed-less players to their first `/sethome` in the world instead of the spawn.

To go back to DwellerCore's random cave spawn, set `spawn.random-cave: true` in DwellerCore's config and `respawn-listener-priority: none` and `spawn-join-listener-priority: none` here, then restart. The newbie kit and welcome message stop as well.

## Ways to end up on the roof

The roof is bedrock around Y -16 to 6, with open air above it up to Y 15.

| Cause | Detail | Fix |
|---|---|---|
| `/spawn`, first join or death before `/setspawn` | Falls back to the world spawn | Run `/setspawn` and `/setspawn newbies` |
| `/home` with no home set | `spawn-if-no-home: true` sends the player to the **world spawn** (not the `/setspawn` one) | Set to `false` on this server |
| `/top` | Teleports to the highest block | Disabled, with its alias `/etop`. Never grant `essentials.top` |
| `/tpr`, `/settpr`, random spawn | Random teleport uses the surface | Disabled, with all aliases (`/etpr`, `/tprandom`, `/etprandom`, `/esettpr`, `/settprandom`, `/esettprandom`). Don't grant `essentials.tpr` or `essentials.settpr` |
| `/jump` | Teleports to the block you look at, plus one block up. Looking at the roof from below can put you in or above it | Don't grant `essentials.jump` |
| Teleport to an unsafe spot (`/tppos`, `/tp x y z`, a blocked home) | The safe-location search checks a small cube, then moves **upward** until it finds air. From solid rock this can pass through the roof | Staff should target caves, not rock. Keep `teleport-safety: true`. Staff in creative or god mode skip the search |
| A home or warp set on the roof | Only possible if the player was already there | `/delhome <name>` or `/delwarp` |
| `/back` after any of the above | Returns to the earlier spot | Same as above |
| `/tphere`, `/tpa`, `/tpo` involving a player on the roof | The other player is moved there | `/tphere` and `/tpo` are staff only |
| `/fly`, creative, `/gamemode` | Staff can fly up | Staff only |

## LuckPerms

LuckPerms is installed with no groups. Essentials permissions are op-only unless the jar says otherwise, so players can use almost nothing until you grant it. The config's `player-commands` list is ignored because `use-bukkit-permissions: true`.

Example:

```
/lp group default permission set essentials.help true
```

Recommended **default** group for a small survival server:

| Area | Permissions |
|---|---|
| Info | `essentials.help`, `essentials.motd`, `essentials.rules`, `essentials.info`, `essentials.list`, `essentials.ping`, `essentials.near`, `essentials.getpos`, `essentials.compass`, `essentials.seen`, `essentials.realname`, `essentials.playtime` |
| Chat | `essentials.msg`, `essentials.r`, `essentials.me`, `essentials.msgtoggle`, `essentials.ignore`, `essentials.mail`, `essentials.mail.send`, `essentials.helpop` |
| Spawn and teleport | `essentials.spawn`, `essentials.tpa`, `essentials.tpahere`, `essentials.tpaccept`, `essentials.tpdeny`, `essentials.tpacancel`, `essentials.tptoggle`, `essentials.back` |
| Homes | `essentials.home`, `essentials.sethome`, `essentials.sethome.multiple` (3 homes), `essentials.delhome`, `essentials.renamehome` |
| Money | `essentials.balance`, `essentials.balancetop`, `essentials.pay` |
| Other | `essentials.suicide`, `essentials.afk`, `essentials.afk.auto` |

Optional: `essentials.home.bed` (`/home bed`), `essentials.warp` and `essentials.warp.list` once you have cave warps, `essentials.nick`, `essentials.back.ondeath` (let `/back` return to the place of death), `essentials.paytoggle`, `essentials.rtoggle`, `essentials.recipe`, `essentials.itemdb`.

Deliberately **not** given:

| Not given | Why |
|---|---|
| `essentials.kit`, `essentials.kits.*` | `kits.yml` ships `tools` with a 10 second delay, so anyone allowed to use it gets stone tools every 10 seconds. The newbie kit does not need the permission |
| `essentials.sell`, `essentials.worth` | The bundled `worth.yml` has prices and there is no money sink. Review prices first |
| `essentials.top`, `essentials.tpr`, `essentials.settpr`, `essentials.jump`, `essentials.bottom` | Can land players on or above the roof |
| `essentials.depth` | Reports depth against sea level, which means nothing here. DwellerCore's HUD shows depth |
| `essentials.tp`, `essentials.tphere`, `essentials.tppos`, `essentials.tpo` | Staff teleports |
| `essentials.sethome.others`, `essentials.home.others`, `essentials.spawn.others`, `essentials.setspawn` | Staff only |
| `essentials.clearinventory`, station commands (`workbench`, `anvil`, ...), `essentials.repair`, `essentials.enchant`, `essentials.hat`, `essentials.item`, `essentials.give`, `essentials.fly`, `essentials.god`, `essentials.heal`, `essentials.feed`, `essentials.speed` | Cheats or item loss |
| `essentials.spawnmob`, `essentials.spawner` | Staff only |
| `essentials.chat.color`, `.format`, `.url`, `essentials.nick.color` | Anti-spam. Grant to trusted ranks later |
| `essentials.keepinv`, `essentials.keepxp` | Remove the death penalty |

Give staff `essentials.*` or a hand-picked list. Do not put `essentials.*` on players.

`essentials.suicide` is the escape for a stuck player. They die and respawn at the spawn point. Items drop where they died.

## Homes

- `essentials.sethome` allows one home. Add `essentials.sethome.multiple` for `sethome-multiple.default` (3). The ranks `essentials.sethome.multiple.vip` (5) and `.staff` (10) also need `essentials.sethome.multiple`. `essentials.sethome.multiple.unlimited` removes the limit.
- A home can be set anywhere. The names `bed` and plain numbers are refused.
- Homes are stored in `plugins/Essentials/userdata/<uuid>.yml`.

## Kits

`plugins/Essentials/kits.yml` ships with five example kits. Only `tools` is used by the config.

| Kit | Delay | Contents |
|---|---|---|
| `tools` | 10 s | Stone sword, shovel, pickaxe, axe. The newbie kit |
| `dtools` | 600 s | Diamond tools with enchants, leather helmet, broadcasts a message |
| `notch` | 6000 s | Notch's head |
| `color` | 6000 s | Book of colors |
| `firework` | 6000 s | Three stacks of fireworks |

Delete the examples you do not want. A delay of `-1` makes a kit one-time. Access is `essentials.kit` plus `essentials.kits.<name>` (lowercase). `/createkit <name> <delay>` writes a kit from your inventory.

## Economy

- Balances start at `starting-balance: 0`. The range is `min-money: -10000` up to `max-money`. Negative balances need `essentials.eco.loan`.
- Money comes from `/sell` and `/eco give`. `worth.yml` lists item prices. Items not listed cannot be sold. Edit the file or use `/setworth`.
- `command-costs` charges money per command, for example `kit-tools: 1500`.
- `/baltop` is cached. `/baltop force` refreshes it.

## Warps and spawn files

- `/setwarp <name>` creates `plugins/Essentials/warps/<name>.yml`. Anyone with `essentials.warp` can use every warp unless `per-warp-permission: true` (then `essentials.warps.<name>`).
- `/setspawn [group]` writes `plugins/Essentials/spawn.yml`. Use plain `/setspawn` (group `default`) and `/setspawn newbies`. Group spawns only apply if players are in a LuckPerms group of that name, so skip them unless you need them.
- The `default` spawn is the fallback for any group without its own spawn. `newbies` falls back to `default`.

## Chat (EssentialsXChat)

There is no separate chat config. Everything is in the `chat:` section of `config.yml`.

| Key | Default | Effect |
|---|---|---|
| `chat.radius` | `0` | Blocks a message travels. `0` is global chat |
| `chat.format` | `<{DISPLAYNAME}> {MESSAGE}` | Chat line. A single string, or a map of `normal`, `question`, `shout` |
| `chat.group-formats` | empty | Format per LuckPerms group, case sensitive |
| `chat.world-aliases` | empty | Short names for `{WORLD}` |
| `chat.shout-default` | `false` | Start players in shout mode |
| `chat.persist-shout` | `false` | Keep shout mode across restarts |
| `chat.question-enabled` | `true` | Allow `?` questions (only matters with a radius) |
| `chat.paper-chat-events` | `true` | Use Paper's chat events. Restart needed |

Placeholders: `{MESSAGE}`, `{USERNAME}`, `{DISPLAYNAME}`, `{NICKNAME}`, `{PREFIX}`, `{SUFFIX}`, `{GROUP}`, `{WORLD}`, `{WORLDNAME}`, `{SHORTWORLDNAME}`, `{TEAMNAME}`, `{TEAMPREFIX}`, `{TEAMSUFFIX}`. `{DISPLAYNAME}` already contains prefix and suffix.

With a radius above 0, `!message` shouts (`essentials.chat.shout`), `?message` asks globally (`essentials.chat.question`), and `essentials.chat.spy` sees everything. `essentials.chat.local` and the three `essentials.chat.receive.*` nodes are true by default. Color, format, magic, RGB and URL codes in chat need `essentials.chat.color`, `.format`, `.magic`, `.rgb` and `.url`. `/toggleshout` needs `essentials.toggleshout`.

DiscordSRV and TAB are also installed. If prefixes show twice or not at all, check them first.

## Spawned mobs

`/spawnmob` (`essentials.spawnmob`, `essentials.spawnmob.<mob>`) is capped by `spawnmob-limit: 10`. Hostile mobs it creates get a DwellerCore level from the depth where they spawn and can roll elite traits, like any other spawn. Passive mobs get no level. `/spawner` changes behave the same way.

## Files in `plugins/Essentials`

| File or folder | Content |
|---|---|
| `config.yml` | Main config. Only `disabled-commands: [top, tpr, settpr]` differs from the default |
| `kits.yml` | Kits |
| `worth.yml` | Sell prices |
| `tpr.yml` | Random teleport settings (unused, `/tpr` is disabled) |
| `spawn.yml` | Spawn points, created by `/setspawn` |
| `userdata/<uuid>.yml` | Per player: homes, balance, mail, last location, mute, jail |
| `warps/*.yml` | One file per warp |
| `jail.yml` | Jails |
| `motd.txt`, `info.txt`, `rules.txt` | Text for `/motd`, `/info`, `/rules` |
| `messages_*.properties` | Language overrides |

## Recommended setup

1. Walk to a safe, lit cave spot. Run `/setspawn` and `/setspawn newbies`. Check with `/spawn`.
2. Edit `config.yml`:

    ```yaml
    disabled-commands:
      - top
      - etop
      - tpr
      - etpr
      - tprandom
      - etprandom
      - settpr
      - esettpr
      - settprandom
      - esettprandom
    spawn-if-no-home: false
    ```

3. Trim `kits.yml` to the kits you want. Decide whether `tools` should be one-time (`delay: -1`).
4. Create the LuckPerms `default` group with the list above.
5. Fill `motd.txt`, `rules.txt` and `info.txt`.
6. Restart, then test with a fresh account: first join, death, `/home`, `/spawn`.

## Problems

| Symptom | Cause | Fix |
|---|---|---|
| Players join or die and end up on the roof | `/setspawn` not set | Set the `default` and `newbies` spawns inside a cave |
| `/home` with no home lands on the roof | `spawn-if-no-home: true` | Set it to `false` |
| "You do not have access to that command" | Fresh LuckPerms group | Grant the nodes above. `/r` needs `essentials.r` |
| Beds do not set the respawn | DwellerCore older than the bed fix | Update DwellerCore. See [Death](#death) |
| First join teleports twice | DwellerCore moves them first, then Essentials | Normal and harmless |
| `/kit tools` gives tools again and again | Delay is 10 seconds | Don't grant kit permissions, or raise the delay |
| `/etop` or `/etpr` still works | Disabled commands match the exact word typed | Add the aliases |
| Chat prefixes are empty | No Vault | Install Vault |
| A config change does nothing | Listener priorities and `paper-chat-events` are read at startup | Restart |

See also [Troubleshooting](../troubleshooting.md), [DwellerCore config](../config-core.md), and the player wiki at <https://craftyplayz.github.io/DwellerMC-wiki/>.

## Links

- [EssentialsX wiki](https://essentialsx.net/wiki/Home.html)
- [Command reference](https://essentialsx.net/wiki/Command-Reference.html)
- [Permissions](https://essentialsx.net/wiki/Permissions.html)
- [Module breakdown](https://essentialsx.net/wiki/Module-Breakdown.html)

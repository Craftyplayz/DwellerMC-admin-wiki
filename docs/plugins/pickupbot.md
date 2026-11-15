# PickupBot

`PickupBot-1.1` by Mateitaa1 (api 1.21). Automatically moves nearby dropped items into the player's inventory. Player page: <https://craftyplayz.github.io/DwellerMC-wiki/plugins/pickupbot/>. Related: [Veinminer](veinminer.md), [CraftyTools](craftytools.md).

## Commands and permissions

Commands `/pickupbot` (alias `/pb`) and `/pickup`. Subcommands: `toggle`, `on`, `off`, `status`, `help`, `reload` (needs `pickupbot.admin`). No argument shows help.

| Node | Default | Effect |
|---|---|---|
| `pickupbot.use` | **true** (everyone) | Use the commands. Does not gate the collecting itself. |
| `pickupbot.admin` | op (includes `pickupbot.use`) | `/pickup reload`. |

LuckPerms: leave as is. Negating `pickupbot.use` only removes the command; the player is still collected for.

## config.yml (`plugins/PickupBot/config.yml`)

| Key | Default | Effect |
|---|---|---|
| `default-enabled` | `true` | State for players not seen since the last server start. |
| `notify-on-join` | `true` | Chat message on join (text hard-coded). |
| `collection-radius` | `5.0` | Box around the player (each direction) scanned for items. |
| `collection-delay` | `10` | Ticks between scans. Do not set 0. |
| `play-sound` | `true` | Quiet pickup sound. |
| `show-particles` | `true` | Small cloud where the item was. |
| `collect-mob-drops` | `true` | **No effect** (the death handler returns either way). |
| `collect-block-drops` | `true` | If `false`, block drops are not tagged to the miner, so any enabled player nearby takes them. It does not switch block pickup off. |
| `collect-player-drops` | `false` | `false`: thrown items are never collected. `true`: collected after 3 seconds. |
| `blacklisted-items` | `DRAGON_EGG, BEDROCK, COMMAND_BLOCK, TNT` | Material names never collected. |
| `blacklisted-blocks` | `SPAWNER` | Same check as items (matched on the item's material name). |
| `messages.*` | see file | **Not used.** Messages are hard-coded. |

`/pickup reload` re-reads the file. On load the file is rewritten with defaults (comments get reformatted).

## How it works

- A repeating task every `collection-delay` ticks adds items in range to the inventory with `addItem`. No pickup delay, **no pickup event**: it ignores WorldGuard pickup rules and other plugins watching pickups. Full inventory: the leftover stays on the ground.
- Ownership: a player's block break (`BlockBreakEvent`, MONITOR, ignoring cancelled) records time and spot. An item spawning within 500 ms and 10 blocks becomes theirs for 10 seconds. Only the owner collects owned items. Unowned items (mob drops, anything else) go to any enabled player in range.
- Death drops: items spawning within 2 seconds and 10 blocks of a dying player are never collected.
- The enabled/disabled choice is kept in memory only (no saving code found): it resets on restart.

## Interactions

- **[CraftyTools](craftytools.md) 3x3 pickaxe:** every area block fires its own break event, so drops are owned by the miner and collected.
- **[Veinminer](veinminer.md):** Veinminer's break events are `BlockBreakEvent` subclasses, so PickupBot counts them. But the miner only collects within `collection-radius`. Drops further away stay on the floor. Fix: Veinminer `mergeItemDrops: true`, and/or a larger `collection-radius` (7 to 8; scan cost grows).
- **Mob drops** go to the nearest enabled player in range, so kills can be stolen.
- **WorldGuard / CoreProtect:** pickups bypass pickup flags and produce no item pickup logs (no event fired).

## Problems

- Items left after veinmining: out of radius or full inventory.
- Toggle reset after restart: expected.
- `collect-mob-drops: false` does nothing; editing `messages` does nothing.

## Links

- <https://github.com/Mateitaa1>

# CoreProtect

| | |
|---|---|
| Version | 24.1 (`branch: development`) |
| Author | Intelli |
| Jar | `CoreProtect-24.1.jar` |
| Soft depends | WorldEdit (installed) |
| Libraries | `org.duckdb:duckdb_jdbc:1.4.5.0` (Paper downloads it on first start, so the server needs internet once) |
| Data folder | `plugins/CoreProtect/` |
| Folia | supported |

CoreProtect records what happens in the world: blocks placed and broken, containers, item drops, kills, chat, commands, sessions, signs. You use it to find out who did something and to roll it back.

## What it does on DwellerMC

- Answers "who dug this out / who opened this chest / who killed this animal".
- Rolls back griefing and restores it.
- Logs through events, so it also logs Essentials commands and WorldEdit changes (`worldedit: true`).

## Commands

Main command `/co`. `/core` and `/coreprotect` are also declared. `/co` is allowed for everyone by default, but almost every subcommand needs its own node, and those default to op.

| Command | Does | Permission |
|---|---|---|
| `/co help` | Command list | `coreprotect.help` |
| `/co inspect` (`/co i`) | Toggle inspector. Click a block to see its history. | `coreprotect.inspect` |
| `/co lookup <params>` (`/co l`) | Search logs. `/co l <page>` changes page, `/co l 2:10` page 2 with 10 lines. | `coreprotect.lookup` |
| `/co near` | Lookup with radius 5 | `coreprotect.lookup.near` |
| `/co rollback <params>` (`/co rb`) | Undo changes | `coreprotect.rollback` |
| `/co restore <params>` (`/co rs`) | Redo rolled-back changes | `coreprotect.restore` |
| `/co undo` | Reverse the last rollback or restore | rollback/restore node |
| `/co purge t:<time> [r:...] [i:...] [e:...] [a:kill] [#optimize]` | Delete old data | `coreprotect.purge` |
| `/co reload` | Reload `config.yml` | `coreprotect.reload` |
| `/co status` | Version and status | `coreprotect.status` |
| `/co consumer` | Pause or resume the write queue. **Console only.** | `coreprotect.consumer` |
| `/co migrate-db <sqlite\|mysql\|duckdb\|clickhouse> [--full-validation]` | Move data to another database. **Console only.** | not stated |
| `/co teleport` | Teleport to a location. Used by clickable results. | `coreprotect.teleport` |
| `/co give` | Declared permission `coreprotect.give` (default false). | `coreprotect.give` |

### Parameters

| Param | Meaning |
|---|---|
| `u:<user>` | Players (`u:Steve,Alex`) or a group like `u:#tnt`. |
| `t:<time>` | Time back: weeks `w`, days `d`, hours `h`, minutes `m`, seconds `s`. Combine: `t:5d2h`. Range: `t:1h-2h`. |
| `r:<radius>` | Blocks around you, or `r:#global`, `r:#<world>`, `r:#worldedit` (`#we`). |
| `a:<action>` | One action, list below. |
| `i:<list>` | Include blocks, items or entities (comma separated). |
| `e:<list>` | Exclude blocks, items, entities or users. |
| `f:<filter>` | Filter chat, command or sign text by prefix (each prefix at least 3 characters). |
| `#preview` | Show a rollback or restore without applying it. |
| `#count` | Only the row count. |
| `#summary` | Count plus per-user material totals. |
| `#verbose` / `#silent` | More or less output during a rollback or restore. |
| `#optimize` | Purge only. See below. |

Actions for `a:`: `block`, `+block`, `-block`, `chat`, `click`, `command`, `container`, `+container`, `-container`, `inventory`, `+inventory`, `-inventory`, `item`, `+item`, `-item`, `kill`, `spawn`, `session`, `+session`, `-session`, `sign`, `username`.

A rollback with no `r:` uses `default-radius` (10). `r:#global` is needed for a world-wide rollback.

!!! warning "Always preview big rollbacks"
    `/co rollback u:Name t:3d r:50 #preview` first. In this world, `r:` is a sphere/cube around you in 3D, so in a 2048-tall world a small radius covers little. Stand at the spot, or use `r:#worldedit` with a WorldEdit selection that spans the Y range you need.

### Purge rules

- In game you can only purge data older than 30 days. From the console the minimum is 24 hours.
- `r:#<world>` limits a purge to one world.
- `r:<n>` works only in game, only with `i:` or `a:kill`.
- `a:kill` cannot be combined with block types in `i:`.
- `#optimize`: not needed for SQLite or DuckDB. On MySQL or ClickHouse it can be very slow.

## Permissions

| Node | Default | Notes |
|---|---|---|
| `coreprotect.co` | **true** | `/co` itself. Everyone has it. |
| `coreprotect.core`, `coreprotect.coreprotect` | false | `/core`, `/coreprotect` aliases. |
| `coreprotect.*` | op | All below except give. |
| `coreprotect.inspect` | op | Inspector. |
| `coreprotect.lookup` | op | Lookup. Children below. |
| `coreprotect.rollback`, `coreprotect.restore` | op | |
| `coreprotect.help`, `coreprotect.status`, `coreprotect.teleport` | op | |
| `coreprotect.purge`, `coreprotect.reload`, `coreprotect.consumer` | op | Admin only. |
| `coreprotect.networking` | op | API networking. |
| `coreprotect.give` | false | |
| `coreprotect.lookup.<type>` | op | `near`, `chat`, `command`, `session`, `username`, `block`, `sign`, `click`, `container`, `inventory`, `item`, `kill`, `spawn`. Granting `coreprotect.lookup` grants all. Use `false` on one child in LuckPerms to block a single lookup type (for example hide `chat` from mods). |

So a normal player can type `/co` and gets "no permission" for everything useful. That is harmless.

## Configuration

The file is created in `plugins/CoreProtect/config.yml` on first start. Check the generated file if a default below differs on your install.

### Database

| Key | Default | Effect |
|---|---|---|
| `database-type` | `duckdb` (new installs) | `duckdb`, `sqlite`, `mysql` or `clickhouse`. Upgraded installs keep `sqlite` or `mysql` from the old `use-mysql`. |
| `use-mysql` | `false` | Old key; only used if `database-type` is missing. |
| `table-prefix` | `co_` | Table prefix. |
| `mysql-host` / `mysql-port` / `mysql-database` / `mysql-username` / `mysql-password` | `127.0.0.1` / `3306` / `database` / `root` / empty | MySQL connection. |
| `clickhouse-host` / `-port` / `-database` / `-username` / `-password` / `-tls` | `127.0.0.1` / `8123` / `default` / `default` / empty / `false` | ClickHouse (needs ClickHouse 26.1+; database must exist). |
| `clickhouse-consumer-delay` | `2500` (min 500) | Write queue delay in ms. |
| `duckdb-memory-limit` | `512MB` | DuckDB buffer limit. Total native memory can be higher. |
| `duckdb-threads` | `3` | Query threads. |
| `duckdb-max-temp-directory-size` | `10GB` | Cap for spill files. |
| `duckdb-consumer-delay` | `2500` (min 500) | Write queue delay in ms. |
| `sqlite-database` | empty (uses `database.db`) | SQLite file name. |
| `enable-ssl` | `false` | SSL for MySQL. |
| `disable-wal` | `false` | SQLite journal setting. |
| `database-lock` | `true` | Prevents two servers using one DB. Must stay on for purge and migration. |
| `maximum-pool-size` | `10` | Connection pool. |

### General

| Key | Default | Effect |
|---|---|---|
| `donation-key` | empty | Donation key. Not needed. |
| `language` | `en` | Message language. |
| `auto-purge` | `false` | Delete data older than this (`30d`, `12w`, `6mo`). `false` = keep forever. |
| `check-updates` | `true` | Check for updates at start. |
| `error-reporting` | `true` | Sends errors to the author. Consider `false` for privacy. |
| `api-enabled` | `true` | Let other plugins use the CoreProtect API. |
| `verbose` | `true` | Detailed output for rollbacks. |
| `default-radius` | `10` | Radius if none given. `0` disables. |
| `max-radius` | `100` | Largest allowed `r:`. `0` disables the limit. `r:#global` always works for permitted users. |
| `hover-events` | `true` | Hover text in results. |
| `log-cancelled-chat` | `true` | Also log chat other plugins cancelled. |
| `hopper-filter-meta` | `false` | |
| `duplicate-suppression` | `true` | Skip duplicate rows. |
| `exclude-tnt` | `false` | |
| `network-debug` | `false` | |
| `unknown-logging` | `false` | |

### Rollback behaviour

| Key | Default | Effect |
|---|---|---|
| `rollback-items` | `true` | Rollbacks also put back items taken from containers. |
| `rollback-entities` | `true` | Rollbacks also restore killed entities and player spawns. |
| `skip-generic-data` | `true` | Do not log things like zombies burning in daylight. |

### What is logged (all `true` unless noted)

| Key | Logs |
|---|---|
| `block-place`, `block-break` | Player placing and breaking blocks |
| `natural-break` | Blocks that fall off when their support breaks (torches, doors, beds) |
| `block-movement` | Falling sand/gravel |
| `pistons` | Blocks moved by pistons |
| `dispensers` | Dispenser placement and removal |
| `block-burn`, `block-ignite` | Fire |
| `fire-extinguish` (default `false`) | Fire put out |
| `explosions` | TNT, creepers |
| `entity-change` | Block changes by entities (endermen) |
| `entity-kills` | Killed entities |
| `entity-spawns` | Player-placed or spawned entities (boats, spawn eggs) |
| `sign-text` | Sign text (if off, rollbacks give blank signs) |
| `buckets` | Bucket use |
| `leaf-decay`, `tree-growth`, `mushroom-growth`, `vine-growth`, `sculk-spread`, `portals` | Natural growth |
| `water-flow`, `lava-flow`, `liquid-tracking` | Liquids. `liquid-tracking` links flows back to the player who placed the source. |
| `item-transactions` | Container, furnace and dispenser item changes |
| `item-drops`, `item-pickups` | Dropped and picked-up items |
| `hopper-transactions` | Hopper transfers |
| `player-interactions` | Doors, buttons, chests opened (lookup only, not rolled back) |
| `player-messages`, `player-commands` | Chat and commands |
| `player-sessions` | Logins and logouts |
| `username-changes` | Name changes |
| `worldedit` | WorldEdit edits |

## Storage

- New installs use **DuckDB** in `plugins/CoreProtect/`. The exact file name is not stated in the docs; check the folder after the first start. SQLite uses `database.db` (default).
- DuckDB uses native memory outside the Java heap (up to `duckdb-memory-limit` plus extra). Keep this in mind when you set `-Xmx`.
- Changing `database-type` does not copy old data. Use `/co migrate-db` (console) for that. The target must be empty.

## Interactions with DwellerMC

- **Y range:** CoreProtect stores block Y as a number. After setup, test it once: place a block at Y -2000 and run `/co i` on it.
- **Radius:** `r:` is 3D. A `max-radius` of 100 is plenty. Rollbacks spanning the full depth need `r:#worldedit` or `r:#global` plus `u:` and `t:`.
- **Log volume:** a cave world means lots of mining. Block-break rows are the biggest table. Plan to enable `auto-purge`.
- **Lava and water:** caves have a lot of flowing liquid. `water-flow` and `lava-flow` can create many rows. Consider turning them off if the database grows quickly (see recommendations).
- **Mob levels:** `entity-kills` logs the entity type, not the level. Elites and leveled mobs are logged like any other mob.
- **World generation:** CoreProtect does not log chunk generation, so DwellerCore/DwellerBiomes/DwellerStructures changes never appear.
- **Other plugins:** Essentials `/give`, `/tp` and so on appear under `a:command`. WorldEdit edits are logged when `worldedit: true`, and can be rolled back with `r:#worldedit`. Block edits by VeinMiner, BlockDisguise, Citizens and the like depend on how those plugins fire events, so test them before relying on rollbacks.
- **LuckPerms:** needed to give staff the nodes. See [LuckPerms](luckperms.md).
- **WorldGuard:** independent. Denied actions are not logged by CoreProtect because they never happen.

## Recommended for this server

1. Give mods `coreprotect.inspect`, `coreprotect.lookup`, `coreprotect.rollback`, `coreprotect.restore`, `coreprotect.help`. Keep `purge`, `reload`, `consumer` for the owner:

```
/lp group mod permission set coreprotect.inspect true
/lp group mod permission set coreprotect.lookup true
/lp group mod permission set coreprotect.rollback true
/lp group mod permission set coreprotect.restore true
/lp group mod permission set coreprotect.help true
```

2. Set `auto-purge: 60d` (or whatever you can afford). The in-game purge command refuses data under 30 days.
3. Consider `water-flow: false`, `lava-flow: false`, `liquid-tracking: false` if the database grows too fast. You lose "who started this lava flow" lookups.
4. Set `error-reporting: false` if you do not want reports sent out.
5. Leave `database-type: duckdb` for a small server. Watch memory with [spark](spark.md).

## Common problems

| Problem | Fix |
|---|---|
| Server fails to enable CoreProtect on first start | DuckDB library download failed. Check internet and `libraries/` folder. Or set `database-type: sqlite`. |
| `/co i` says no permission | The player needs `coreprotect.inspect` (op default). |
| Lookup shows nothing | You are too far away, or `r:` is smaller than the area. Use `t:` and `u:`, or `r:#global`. |
| Rollback did not restore items | `rollback-items` is `false`, or the container was not logged. |
| Data lost on shutdown | Do not kill the server process. The consumer queue writes in the background; stop the server normally. |

## Links

- [Commands](https://docs.coreprotect.net/commands/)
- [Permissions](https://docs.coreprotect.net/permissions/)
- [Configuration](https://docs.coreprotect.net/config/)

Related: [Index](../index.md), [Technical notes](../technical.md), [LuckPerms](luckperms.md), [WorldEdit](worldedit.md).

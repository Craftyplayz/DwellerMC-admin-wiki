# LuckPerms

| | |
|---|---|
| Version | 5.5.87 (Bukkit jar `LuckPerms-Bukkit-5.5.87.jar`) |
| Load | `STARTUP`, and it loads before Vault (`loadbefore: [Vault]`) |
| Soft depends | LilyPad-Connect, ViaVersion (neither is installed) |
| Hard depends | none |
| Data folder | `plugins/LuckPerms/` (created on first start) |

LuckPerms is the permissions plugin. It stores users, groups, tracks and prefixes. It decides who may run which command. Nothing else on the server hands out permissions, so every other plugin page in this wiki says "give it with LuckPerms".

!!! warning "Fresh install = nobody has any LuckPerms access except ops"
    The server starts with no groups and no permissions. Players only have permissions whose `plugin.yml` default is `true`. Ops can use LuckPerms commands because `commands-allow-op: true` (default). See [First-time setup](#first-time-setup).

## What it does on DwellerMC

- Gives or removes command permissions per player or per group.
- Groups can inherit from other groups (`parent add`).
- Optional prefixes and suffixes (used by chat and tab plugins).
- Keeps an action log of every permission change (`/lp log`).
- Has a web editor (`/lp editor`).

It does not know about depth, mob levels or the cave world. The only link is that you may use a [context](#contexts) such as `world=world` to limit a permission to one world.

## Commands

The command is `/luckperms`. Aliases: `/lp`, `/perm`, `/perms`, `/permission`, `/permissions`. The command is declared in `plugin.yml` without a `permission:` field. LuckPerms checks its own `luckperms.*` nodes inside the command. Typing `/lp` shows only the commands the sender may use.

Key: `<required>`, `[optional]`. Use quotes around arguments that contain spaces. `[context...]` means optional `key=value` pairs, for example `world=world` or `server=global`.

### General commands

| Command | What it does | Permission |
|---|---|---|
| `/lp` | Shows plugin version and help | none needed to see the version |
| `/lp sync` | Reloads all data from storage | `luckperms.sync` |
| `/lp networksync` | Sync and push to other servers | `luckperms.sync` |
| `/lp info` | Storage type, messaging, counts | `luckperms.info` |
| `/lp editor [type]` | Opens the web editor | `luckperms.editor` |
| `/lp verbose <on\|record\|off\|upload> [filter]` | Live view or recording of every permission check | `luckperms.verbose` |
| `/lp tree [scope] [player]` | Tree of all permissions LuckPerms has seen | `luckperms.tree` |
| `/lp search [comparison] <permission>` | Finds users and groups that hold a node | `luckperms.search` |
| `/lp import <file\|code --upload> [--replace]` | Imports a data export | `luckperms.import` |
| `/lp export <file> [--upload]` | Exports all data to a file | `luckperms.export` |
| `/lp reloadconfig` | Reloads `config.yml` (some options still need a restart) | `luckperms.reloadconfig` |
| `/lp bulkupdate ...` | Mass edit with constraints | console only |
| `/lp translations` | Install or update language files | `luckperms.translations` |
| `/lp creategroup <group> [weight] [displayname]` | New group | `luckperms.creategroup` |
| `/lp deletegroup <group>` | Delete a group | `luckperms.deletegroup` |
| `/lp listgroups` | List groups | `luckperms.listgroups` |
| `/lp createtrack <track>` / `deletetrack` / `listtracks` | Manage tracks (promotion ladders) | `luckperms.createtrack` / `.deletetrack` / `.listtracks` |

`/lp verbose command <name> <command>` needs `luckperms.verbose.commandother`.

### User commands (`/lp user <user> ...`)

`<user>` is a name or a UUID.

| Subcommand | Permission |
|---|---|
| `info` | `luckperms.user.info` |
| `editor` | `luckperms.user.editor` |
| `promote <track> [context...]`, `demote <track> [context...]` | `luckperms.user.promote`, `luckperms.user.demote` |
| `showtracks` | `luckperms.user.showtracks` |
| `clear [context...]` (removes all of the user's data) | `luckperms.user.clear` |
| `clone <user>` | `luckperms.user.clone` |
| `permission ...` | see below, `luckperms.user.permission.<sub>` |
| `parent ...` | `luckperms.user.parent.<sub>` |
| `meta ...` | `luckperms.user.meta.<sub>` |

### Group commands (`/lp group <group> ...`)

| Subcommand | Permission |
|---|---|
| `info`, `editor` | `luckperms.group.info`, `luckperms.group.editor` |
| `listmembers [page]` | `luckperms.group.listmembers` |
| `setweight <weight>` | `luckperms.group.setweight` |
| `setdisplayname <name>` | `luckperms.group.setdisplayname` |
| `showtracks` | `luckperms.group.showtracks` |
| `clear [context...]` | `luckperms.group.clear` |
| `rename <new name>`, `clone <name>` | `luckperms.group.rename`, `luckperms.group.clone` |
| `permission ...`, `parent ...`, `meta ...` | `luckperms.group.permission.<sub>` etc. |

### Permission subcommands (`/lp user|group <name> permission ...`)

| Subcommand | Effect |
|---|---|
| `info` | List nodes |
| `set <node> <true/false> [context...]` | Add or change a node. `false` negates it. |
| `unset <node> [context...]` | Remove a node |
| `settemp <node> <true/false> <duration> [modifier] [context...]` | Timed node, e.g. `1d12h` |
| `unsettemp <node> [duration] [context...]` | Remove a timed node |
| `check <node>` | Does this user or group have the node? |
| `clear [context...]` | Remove all nodes |

### Parent subcommands (`/lp user|group <name> parent ...`)

`info`, `set <group>` (replaces all other parents), `add <group>`, `remove <group>`, `settrack <track> <group>`, `addtemp <group> <duration> [modifier]`, `removetemp <group> [duration]`, `clear`, `cleartrack <track>`, `switchprimarygroup <group>` (users only).

`parent add` is how you put a player in a group.

### Meta subcommands

`info`, `set <key> <value>`, `unset <key>`, `settemp`, `unsettemp`, `addprefix <priority> <prefix>`, `addsuffix`, `setprefix [priority] <prefix>`, `setsuffix`, `removeprefix <priority> [prefix]`, `removesuffix`, the `*temp*` versions of those, and `clear`. Permission pattern: `luckperms.<user|group>.meta.<sub>`.

### Track commands (`/lp track <track> ...`)

`info`, `editor`, `append <group>`, `insert <group> <position>`, `remove <group>`, `clear`, `rename <name>`, `clone <name>`. Permission pattern: `luckperms.track.<sub>`.

### Log commands (`/lp log ...`)

`recent [user] [page]`, `search <query> [page]`, `notify [on|off]`, `userhistory <user> [page]`, `grouphistory <group> [page]`, `trackhistory <track> [page]`. Permission pattern: `luckperms.log.<sub>`.

## Permission nodes that matter

LuckPerms' own nodes follow the pattern above. The useful ones:

| Node | Gives |
|---|---|
| `luckperms.*` | Everything in LuckPerms. Give to yourself from the console first (see below). |
| `luckperms.autoop` | Only used when `auto-op: true`. Makes the holder an op. |
| `luckperms.log.notify` | Receives chat notices when someone edits permissions (`log-notify: true`). |
| `luckperms.verbose` | Use `/lp verbose`. |
| `luckperms.editor`, `luckperms.applyedits` | Open the web editor and apply its changes. |
| `luckperms.user.parent.add` + `luckperms.user.parent.remove` | Enough for a moderator to rank players without being able to write new permissions. |

!!! note "Wildcards"
    With `apply-wildcards: true` (default) `luckperms.*` and `luckperms.user.*` work. A node set to `false` on a more specific level wins over a broader `true`.

### Argument-based command permissions

If `argument-based-command-permissions: true`, LuckPerms runs extra checks so you can restrict which groups or players someone may edit. Examples from the wiki:

- `luckperms.modify.group.<group name>` lets a person edit one group only.
- `luckperms.modify.user.self` / `luckperms.modify.user.others`, `luckperms.view.user.self` / `luckperms.view.user.others` control which users can be edited or viewed.

Default is `false`. Turn it on only if you have staff you do not fully trust with `/lp`.

## First-time setup

1. In the **server console** (not in game): `lp user <your name> permission set luckperms.* true`. Ops already work through `commands-allow-op`, but a real permission is safer than relying on op.
2. Create the groups. The `default` group exists automatically and every player is in it. Do not rename it. Use `/lp group default setdisplayname Player` if you want another visible name.
3. Create staff groups and give them inheritance. Example:

```
/lp creategroup mod
/lp creategroup admin
/lp group mod parent add default
/lp group admin parent add mod
/lp group mod setweight 50
/lp group admin setweight 100
/lp user <name> parent add mod
```

4. Check what a command needs: `/lp verbose record <player>`, let them run the command, then `/lp verbose upload`.

The [per-plugin pages](../index.md) list the nodes to give. A starting layout for the plugins installed in this batch:

| Group | Suggested nodes |
|---|---|
| `mod` | `coreprotect.inspect`, `coreprotect.lookup`, `coreprotect.rollback`, `coreprotect.restore`, `coreprotect.help`, `invseeplusplus.invsee.view`, `invseeplusplus.endersee.view`, `worldguard.region.info.*`, `worldguard.region.list` |
| `admin` | `coreprotect.*`, `invseeplusplus.view`, `invseeplusplus.edit`, `worldedit.*`, `worldguard.region.*` style nodes (see [WorldGuard](worldguard.md)), `spark` |

Sample commands: `/lp group mod permission set coreprotect.inspect true`. These are suggestions, not defaults.

## Contexts

A context limits a node to a situation. Built-in keys on Bukkit: `server`, `world`, `gamemode`, `dimension-type`. Example, only in the DwellerMC world:

```
/lp group mod permission set worldedit.region.set true world=world
```

`world` has the same name as `level-name` in `server.properties` (`world` on the test server).

## Configuration: `plugins/LuckPerms/config.yml`

Defaults for 5.5.87. LuckPerms does not rewrite the file when new options arrive. A missing option uses its built-in default. Every option can also be given as a Java system property `-Dluckperms.<key>` or an environment variable `LUCKPERMS_<KEY>` (dashes become underscores, dots become underscores).

### Essentials

| Key | Default | Effect |
|---|---|---|
| `server` | `global` | Server name used for server-specific permissions. Keep `global` on a single server. |
| `use-server-uuid-cache` | `false` | Use the server's own UUID cache if LuckPerms has no record. |

### Storage

| Key | Default | Effect |
|---|---|---|
| `storage-method` | `h2` | One of `mysql`, `mariadb`, `postgresql`, `sqlite`, `h2`, `json`, `yaml`, `hocon`, `mongodb`. |
| `data.address` | `localhost` | Database host (`host:port` for a non-default port). |
| `data.database` | `minecraft` | Database name. |
| `data.username` / `data.password` | `root` / empty | Credentials. |
| `data.pool-settings.maximum-pool-size` | `10` | Max DB connections. |
| `data.pool-settings.minimum-idle` | `10` | Idle connections kept. |
| `data.pool-settings.maximum-lifetime` | `1800000` | Connection lifetime in ms (30 min). |
| `data.pool-settings.keepalive-time` | `0` | Keepalive ping interval (0 = off). |
| `data.pool-settings.connection-timeout` | `5000` | ms to wait for a connection. |
| `data.pool-settings.properties.useUnicode` / `characterEncoding` | `true` / `utf8` | JDBC properties. |
| `data.table-prefix` | `luckperms_` | SQL table prefix. |
| `data.mongodb-collection-prefix` | empty | MongoDB only. |
| `data.mongodb-connection-uri` | empty | MongoDB only, replaces address/credentials. |
| `split-storage.enabled` | `false` | Use different backends per data type. |
| `split-storage.methods.user/group/track/uuid/log` | `h2` each | Backend per data type when split is on. |

### Sync and messaging (only useful with several servers)

| Key | Default | Effect |
|---|---|---|
| `sync-minutes` | `-1` | Periodic reload from storage. `-1` = off. |
| `watch-files` | `true` | With file storage (json, yaml, hocon), reload when the files change. |
| `messaging-service` | `auto` | `auto`, `sql`, `notsql`, `pluginmsg`, `lilypad`, `redis`, `rabbitmq`, `nats`, `none`. |
| `auto-push-updates` | `true` | Push changes made by command to other servers. |
| `push-log-entries` | `true` | Send log entries to other servers. |
| `broadcast-received-log-entries` | `true` | Show log entries received from other servers. |
| `redis.*` | disabled | `enabled: false`, `address: localhost`, `username`, `password`, `sentinel.*` (enabled false, master `mymaster`, addresses `localhost:26379`). |
| `nats.*` | disabled | `enabled: false`, `address: localhost`, `username`, `password`, `token`. |
| `rabbitmq.*` | disabled | `enabled: false`, `address: localhost`, `vhost: '/'`, `username: guest`, `password: guest`. |

### Behaviour

| Key | Default | Effect |
|---|---|---|
| `temporary-add-behaviour` | `deny` | Adding a duplicate temporary node: `deny` (fail), `accumulate` (add durations), `replace` (keep longest). |
| `primary-group-calculation` | `parents-by-weight` | `stored`, `parents-by-weight`, `all-parents-by-weight`. Decides the primary group shown to other plugins. |
| `argument-based-command-permissions` | `false` | Fine-grained edit rights, see above. |
| `require-sender-group-membership-to-modify` | `false` | A sender may only edit groups they are a member of. |
| `log-notify` | `true` | Tell staff with `luckperms.log.notify` about changes. |
| `log-notify-filtered-descriptions` | empty | Hide log lines containing these texts. |
| `auto-install-translations` | `true` | Download language files on start. Needs internet. |
| `meta-formatting.prefix` / `.suffix` | `format: [highest]`, `duplicates: first-only`, spacers empty except `middle-spacer: " "` | How several prefixes or suffixes combine. Stacking is documented at luckperms.net/wiki/Prefix-&-Suffix-Stacking. |

### Permission calculation

| Key | Default | Effect |
|---|---|---|
| `inheritance-traversal-algorithm` | `depth-first-pre-order` | Also `breadth-first`, `depth-first-post-order`. |
| `post-traversal-inheritance-sort` | `false` | Re-sort groups by weight after traversal. |
| `context-satisfy-mode` | `at-least-one-value-per-key` | Or `all-values-per-key`. |
| `disabled-contexts` | empty | Context keys to ignore. |
| `include-global` | `true` | Apply permissions not tied to a server. Do not set `false` while `server: global`. |
| `include-global-world` | `true` | Apply permissions not tied to a world. |
| `apply-global-groups` | `true` | Follow groups not tied to a server. |
| `apply-global-world-groups` | `true` | Follow groups not tied to a world. |
| `meta-value-selection-default` | `inheritance` | Also `highest-number`, `lowest-number`. |
| `meta-value-selection` | empty | Per meta key override. |
| `apply-wildcards` | `true` | `*` wildcards. |
| `apply-sponge-implicit-wildcards` | `false` | Sponge only, ignore. |
| `apply-default-negated-permissions-before-wildcards` | `false` | Order of negated defaults vs wildcards. |
| `apply-regex` | `true` | Regex nodes (`r=...`). |
| `apply-shorthand` | `true` | Shorthand nodes such as `a.b.(c\|d)`. |
| `apply-bukkit-child-permissions` | `true` | Honour the `children:` in other plugins' `plugin.yml`. |
| `apply-bukkit-default-permissions` | `true` | Honour `default: true/op` in other plugins' `plugin.yml`. **This is why players already have `coreprotect.co`.** |
| `apply-bukkit-attachment-permissions` | `true` | Honour permissions other plugins attach at runtime. |
| `disabled-context-calculators` | `[]` | Names of calculators to switch off. |
| `world-rewrite` | empty | World aliases, e.g. `world_nether: world`. |
| `group-weight` | empty | Fixed weights per group name, in the config instead of by command. |

### Ops and Vault

| Key | Default | Effect |
|---|---|---|
| `enable-ops` | `true` | Vanilla op system works. If `false`, everyone is de-opped and `/op` is disabled. |
| `auto-op` | `false` | Anyone with `luckperms.autoop` becomes op (checked on join and world change). Forces `enable-ops` off. |
| `commands-allow-op` | `true` | Ops may use `/lp` without a node. |
| `vault-unsafe-lookups` | `false` | Allow Vault lookups on the main thread for offline players. |
| `vault-group-use-displaynames` | `true` | Vault group names are display names. |
| `vault-npc-group` | `default` | Group given to NPC players for Vault. |
| `vault-npc-op-status` | `false` | Treat NPCs as ops for Vault. |
| `use-vault-server` / `vault-server` | `false` / `global` | Server name used for Vault calls. |
| `vault-include-global` | `true` | Include global nodes in Vault lookups. |
| `vault-ignore-world` | `false` | Ignore world context for Vault. |

### Miscellaneous

| Key | Default | Effect |
|---|---|---|
| `debug-logins` | `false` | Log login-processing debug lines. |
| `allow-invalid-usernames` | `false` | Allow odd usernames (Geyser-style). |
| `skip-bulkupdate-confirmation` | `false` | `/lp bulkupdate` runs without the confirm step. |
| `disable-bulkupdate` | `false` | Disable bulk update. |
| `prevent-primary-group-removal` | `false` | Block removing a user's primary group. |
| `update-client-command-list` | `true` | Refresh the client's tab-complete list when permissions change. |
| `register-command-list-data` | `true` | Register permission data so clients hide commands they cannot use. |
| `resolve-command-selectors` | `false` | Resolve `@p` style selectors inside `/lp` arguments. |
| `commands-read-only-mode.players` / `.console` | `false` / `false` | Block data-changing `/lp` commands for that sender type. |
| `disable-luckperms-commands.players` / `.console` | `false` / `false` | Turn the `/lp` command off for that sender type. |

## Data files

| Path | What |
|---|---|
| `plugins/LuckPerms/config.yml` | Config. |
| `plugins/LuckPerms/luckperms-h2.mv.db` | The H2 database (default). Holds users, groups, tracks, log. Back this file up. |
| `plugins/LuckPerms/libs/` | Downloaded libraries (the H2 driver is downloaded on first start, so the server needs internet once). |
| `plugins/LuckPerms/translations/` | Language files. |
| `plugins/LuckPerms/contexts.json` | Optional static contexts. |

Only players who have non-default data are stored. A normal player with no extra permissions does not need a row.

## Interactions with other plugins

- **All other plugins:** LuckPerms replaces the default permission lookups. Without LuckPerms nodes, a plugin that has `default: op` for a node is op-only. A node with `default: true` is open to everyone (CoreProtect `coreprotect.co`, for example).
- **Vault:** no Vault jar is installed (the `Vaults-2.2.1.jar` in `plugins/` is a different plugin). LuckPerms only hooks Vault if it is added.
- **EssentialsX / EssentialsXChat / TAB / namecolor:** they read prefixes, suffixes and groups from LuckPerms when they support it.
- **WorldEdit:** WorldEdit on Bukkit reads permissions through the normal Bukkit permission check, so LuckPerms nodes work. `wepif.yml` in the server root is only used for old standalone setups.
- **spark, WorldGuard, CoreProtect, InvSee++:** see their pages. InvSee++ soft-depends on LuckPerms and can use it to look up offline player UUIDs (strategy `permission plugin`).
- **DwellerCore, DwellerBiomes, DwellerStructures:** no commands or permissions, nothing to set up.
- **[DwellerAdmin](../config-admin.md):** `dwelleradmin.use` (default `op`) opens `/admin`. Give it only to staff. It does not get a player in during maintenance, only op does.

## Recommended for this server

- Keep `storage-method: h2`. One server, no need for MySQL.
- Keep `commands-allow-op: true` until your own `luckperms.*` is confirmed. Afterwards, setting it to `false` and removing the op status of everyone but the owner is safer.
- Do not use `*` for non-owners. Ops and holders of `*` bypass most protection checks (WorldGuard region protection, for example).
- Back up `luckperms-h2.mv.db` before big changes, or use `/lp export backup`.
- Turn on `log-notify` (already on) and give the owner `luckperms.log.notify`.

## Common problems

| Problem | Fix |
|---|---|
| "You do not have permission" for a command, but the player has the node | Check the node name with `/lp verbose record <player>`. Check the context (`world=...`). A `false` on a parent group wins over a `true`. |
| Player is not in the expected group after `parent add` | `/lp user <name> info`. Check the group weight and `primary-group-calculation`. |
| Locked out of `/lp` | Run `lp user <name> permission set luckperms.* true` in the console. |
| Changes made in the web editor do not apply | Run the `/lp applyedits <code>` command the editor shows, from an account with `luckperms.applyedits`. |
| Changes lost after restart | Check the console for H2 errors. The `.mv.db` file must be writable. |

## Links

- [LuckPerms wiki](https://luckperms.net/wiki)
- [Command usage](https://luckperms.net/wiki/Command-Usage)
- [Configuration](https://luckperms.net/wiki/Configuration)
- [Permissions list](https://luckperms.net/wiki/Permissions)

Related: [Index](../index.md), [Install](../install.md), [Troubleshooting](../troubleshooting.md).

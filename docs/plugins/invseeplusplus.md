# InvSee++

| | |
|---|---|
| Version | 0.31.22 |
| Jar | `InvSee++.jar` |
| Plugin name | `InvSeePlusPlus` |
| Author | Jannyboy11 |
| Soft depends | Vault, PerWorldInventory, Multiverse-Inventories, LuckPerms, GroupManager, BungeePerms, UltraPermissions |
| Folia | not supported |
| Data folder | `plugins/InvSeePlusPlus/` |

InvSee++ lets staff open and edit the inventory and ender chest of any player, online or offline, even a player who has never joined. Paper 26.3 is listed as supported (Tier 2: fixes on request) and Java 25 is required for 26.x, which matches this server.

## Commands

| Command | Aliases | Does | Permission | Default |
|---|---|---|---|---|
| `/invsee <name\|uuid>` | `/inventorysee`, `/isee` | Open the target's inventory | `invseeplusplus.invsee.view` | op |
| `/endersee <name\|uuid>` | `/enderchestsee`, `/esee` | Open the target's ender chest | `invseeplusplus.endersee.view` | op |
| `/invseeplusplusreload` | `/isppreload`, `/invsee++reload`, `/is++reload` | Reload `config.yml` | `invseeplusplus.reload` | op |

The inventory window shows main slots, armor, off-hand, and the open crafting grid, cursor item and similar "personal" slots of an online player.

The extra commands `/invgive`, `/endergive`, `/invclear`, `/enderclear`, `/invclone`, `/enderclone` belong to separate addon jars (InvSee++_Give, _Clear, _Clone). Only the main jar is installed here, so these commands do not exist.

## Permissions

| Node | Default | Effect |
|---|---|---|
| `invseeplusplus.invsee.view` | op | `/invsee` read-only. Also grants tab-complete. |
| `invseeplusplus.invsee.edit` | op | Edit the inventory. Includes view. |
| `invseeplusplus.endersee.view` | op | `/endersee` read-only. |
| `invseeplusplus.endersee.edit` | op | Edit the ender chest. Includes view. |
| `invseeplusplus.view` | none | Both view nodes. |
| `invseeplusplus.edit` | none | Both edit nodes. |
| `invseeplusplus.tabcomplete` | op | Tab-complete player names. |
| `invseeplusplus.reload` | op | Reload. |
| `invseeplusplus.exempt.invsee` | false | Holder's inventory cannot be opened. |
| `invseeplusplus.exempt.endersee` | false | Holder's ender chest cannot be opened. |
| `invseeplusplus.exempt` | false | Both exempt nodes. |
| `invseeplusplus.bypass-exempt.invsee` / `.endersee` / `invseeplusplus.bypass-exempt` | false | Ignore the exemptions above. |
| `invseeplusplus.*` | false | All of the above plus the addon nodes. |

Players have none of these by default. View alone shows items without changing them.

## Configuration: `plugins/InvSeePlusPlus/config.yml`

| Key | Default | Effect |
|---|---|---|
| `tabcomplete-offline-players` | `true` | Tab-complete offline names. On Paper this runs off the main thread. |
| `enable-offline-player-support` | `true` | Open inventories of offline players. |
| `enable-unknown-player-support` | `true` | Open inventories of players who never joined. |
| `titles.inventory` | `<player>'s inventory` | Window title. |
| `titles.enderchest` | `<player>'s enderchest` | Window title. |
| `templates.inventory` | 6 rows (see below) | Slot layout for the inventory window. |
| `templates.enderchest` | 6 rows, `e_00` to `e_53` | Slot layout for the ender chest window. |
| `logging.granularity` | `LOG_ON_CLOSE` | `LOG_NEVER`, `LOG_ON_CLOSE` or `LOG_EVERY_CHANGE`. |
| `logging.output` | `[PLUGIN_LOG_FILE]` | Any of `SERVER_LOG_FILE`, `PLUGIN_LOG_FILE`, `SPECTATOR_LOG_FILE`, `CONSOLE`. |
| `logging.format-server-log-file` / `format-plugin-log-file` / `format-spectator-log-file` / `format-console` | multi-line | Text templates. Placeholders: `<date>`, `<time>`, `<level>`, `<spectator_uuid>`, `<spectator_name>`, `<taken>`, `<given>`, `<target>`. |
| `placeholder-palette` | `glass panes` | Filler items for unused slots: `glass panes`, `icons` or `empty`. |
| `uuid-resolve-strategies` | commented out | Order used to find a UUID from a name. Options: `online player`, `logged out players cache`, `paper offline player cache`, `permission plugin`, `proxy`, `mojang rest api call`, `electroid mojang api call`, `player data save files`, `spoof`. |
| `username-resolve-strategies` | commented out | Order for UUID to name. |

Default inventory template:

```
i_00 i_01 i_02 i_03 i_04 i_05 i_06 i_07 i_08
i_09 i_10 i_11 i_12 i_13 i_14 i_15 i_16 i_17
i_18 i_19 i_20 i_21 i_22 i_23 i_24 i_25 i_26
i_27 i_28 i_29 i_30 i_31 i_32 i_33 i_34 i_35
a_b  a_l  a_c  a_h  oh   b    s    c    _
p_00 p_01 p_02 p_03 p_04 p_05 p_06 p_07 p_08
```

`i_xx` = inventory slot, `a_b/l/c/h` = boots, leggings, chest, helmet, `oh` = off-hand, `b` = body, `s` = saddle, `c` = cursor, `p_xx` = crafting/anvil/etc. slots, `_` = unused. Ender chest: `e_xx`.

## Data files

- `plugins/InvSeePlusPlus/config.yml`.
- Log file for `PLUGIN_LOG_FILE` in the plugin folder.
- Offline inventories are read and written in the world's `playerdata` files. The plugin keeps no database.
- The plugin uses bStats and FastStats. To opt out, edit their config files in `plugins/bStats` and `plugins/fastStats`.

## Interactions

- **LuckPerms:** needed for the permission nodes. InvSee++ can also use it to resolve offline names to UUIDs (the `permission plugin` strategy). See [LuckPerms](luckperms.md).
- **Offline edits:** editing a player's inventory while they are offline writes their save file. If they join at the same moment the change can be lost. Prefer view-only for offline players.
- **CoreProtect:** keep InvSee++ edit rights to a few trusted admins and leave InvSee++ logging on (`LOG_ON_CLOSE` records what was taken and given).
- **DwellerCore gear and perks:** leveled gear or perk items sit in normal item slots and can be moved like any item. Nothing special is needed.

## Recommended for this server

```
/lp group mod permission set invseeplusplus.invsee.view true
/lp group mod permission set invseeplusplus.endersee.view true
/lp group admin permission set invseeplusplus.edit true
/lp group admin permission set invseeplusplus.bypass-exempt true
/lp user <owner> permission set invseeplusplus.exempt true
```

- Keep logging on. `LOG_ON_CLOSE` with `PLUGIN_LOG_FILE` is the default.
- Give `invseeplusplus.exempt` to the owner and any admin who should not be inspected.
- Do not give `invseeplusplus.*` to anyone but the owner.

## Common problems

| Problem | Fix |
|---|---|
| "You do not have permission" | Players have no default access. Grant the view node. |
| Plugin does not load | Needs Java 25 on Paper 26.x. Check the console for the supported version. |
| Name not found | Offline name never seen. Use the UUID, or check `enable-unknown-player-support`. |
| Edits to an online player do not stick | Give the `edit` node, view is read-only. |

## Links

- [InvSee++ on GitHub](https://github.com/Jannyboy11/InvSee-plus-plus)

Related: [Index](../index.md), [LuckPerms](luckperms.md), [CoreProtect](coreprotect.md).

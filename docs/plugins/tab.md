# TAB

**Version 6.2.1** by NEZNAMY. Controls the player list (header, footer, names, sorting, ping), name tags above players, below-name text, boss bars and sidebar scoreboards. Player-facing summary: [Tab list](https://craftyplayz.github.io/DwellerMC-wiki/plugins/tab-list/).

!!! note "Use the universal jar"
    Each TAB release ships two jars. On Paper 26.3 use `TAB.v<version>.jar`, not the one named "Paper 1.20.5 - 1.21.4". The server runs `TAB.v6.2.1.jar`.

## What it does

| Feature (config section) | Default | Effect |
|---|---|---|
| `header-footer` | enabled | Header and footer of the tab list, with animations and placeholders. |
| `tablist-name-formatting` | enabled | Prefix/name/suffix in the tab list from `groups.yml` / `users.yml`. |
| `scoreboard-teams` | enabled | Name tags above heads (prefix/suffix, collision, sorting). Also what sorts the tab list. |
| `playerlist-objective` | enabled | Number shown beside each name in the tab list (default `%ping%`). |
| `belowname-objective` | disabled | Text under the name tag (default health). |
| `bossbar` | disabled | Custom boss bars. |
| `scoreboard` | disabled | Sidebar scoreboard. |
| `layout` | disabled | Fixed-slot tab list (80 slots). |
| `ping-spoof`, `global-playerlist`, `per-world-playerlist`, `proxy-support`, `mysql` | disabled | Network features; not needed on a single server. |

## Commands

Main command `/tab` (plus `/bossbar` and `/sb` toggles when those features are on).

| Command | Permission | Effect |
|---|---|---|
| `/tab reload` | `tab.reload` | Reload all files. |
| `/tab cpu` | `tab.cpu` | CPU usage of TAB. |
| `/tab dump <player>` | `tab.dump` | Debug dump for a bug report. |
| `/tab groups`, `/tab group <g>` | `tab.grouplist`, `tab.groupinfo` | List/show groups. |
| `/tab group <g> <property> <value...>` / `/tab player <p> ...` | `tab.change.*` | Set tabprefix, tabsuffix, tagprefix, tagsuffix, customtabname, etc. |
| `/tab group\|player <name> remove` | `tab.remove` | Clear data. |
| `/tab parse <player> <placeholder>` | `tab.parse` | Test a placeholder. |
| `/tab setcollision <player> <true/false>` | `tab.setcollision` | Per-player collision. |
| `/tab nametag <show\|opaque\|hide\|toggle> [player] [viewer] [-s]` and the `...view` variants | `tab.nametag.toggle`, `tab.nametag.toggle.other` | Hide/show name tags. |
| `/tab bossbar [on\|off\|toggle]`, `send`, `announce` | `tab.bossbar.toggle`, `tab.send.bar`, `tab.announce.bar` | Boss bar control. |
| `/tab scoreboard [on\|off\|toggle]`, `show`, `announce` | `tab.scoreboard.toggle(.other)`, `tab.scoreboard.show(.other)`, `tab.announce.scoreboard` | Sidebar control. |
| `/tab mysql upload\|download` | `tab.mysql.upload`, `tab.mysql.download` | Sync files with MySQL. |

## Permissions

All are `default: op`: `tab.admin` (everything), `tab.tabcomplete`, `tab.reload`, `tab.cpu`, `tab.dump`, `tab.groupinfo`, `tab.grouplist`, `tab.parse`, `tab.setcollision`, `tab.remove`, `tab.change.*`, `tab.announce.bar`, `tab.bossbar.toggle`, `tab.send.bar`, `tab.mysql.download`, `tab.mysql.upload`, `tab.nametag.toggle`, `tab.nametag.toggle.other`, `tab.announce.scoreboard`, `tab.scoreboard.toggle`, `tab.scoreboard.toggle.other`, `tab.scoreboard.show`, `tab.scoreboard.show.other`, `tab.staff`, `tab.group.*`, `tab.bypass`, `tab.seevanished`, `tab.spectatorbypass`.

Plus meaning of three special ones: `tab.staff` makes a player count in `%staffonline%`; `tab.seevanished` lets staff see vanished players in the list; `tab.bypass` skips TAB's tab-list changes for that player (use for debugging).

**LuckPerms default group:** players need nothing for normal viewing. Only if you enable the boss bar or scoreboard and want players to switch them off, grant `tab.bossbar.toggle` / `tab.scoreboard.toggle` (and `tab.nametag.toggle`). Give your staff group `tab.staff` and `tab.seevanished`.

## Config reference

Files in `plugins/TAB/`: `config.yml`, `groups.yml`, `users.yml`, `animations.yml`, `messages.yml`.

### config.yml

| Key | Default | Effect |
|---|---|---|
| `header-footer.enabled` | true | Tab list header/footer. |
| `header-footer.designs.default.header/footer` | Placeholder text ("Server name", `www.domain.com`, `%online%`, `%staffonline%`, `%ping%`, `%memory-used%`) | **Edit before going live**: it ships with demo text. |
| `header-footer.designs.world-test` | condition `%world%=test` | Example per-world design. Delete. |
| `tablist-name-formatting.enabled` | true | Names in the list. |
| `*.disable-condition` | `'%world%=disabledworld'` | Condition turning the feature off. Example only. |
| `scoreboard-teams.enabled` | true | Name tag teams. |
| `scoreboard-teams.enable-collision` | true | Players collide. |
| `scoreboard-teams.invisible-nametags` | false | Hide all player name tags. |
| `scoreboard-teams.sorting-types` | `GROUPS:owner,admin,mod,helper,builder,vip,default` then `PLACEHOLDER_A_TO_Z:%player%` | Tab list order. Change the group names to yours. |
| `scoreboard-teams.case-sensitive-sorting` | true | Sorting rule. |
| `scoreboard-teams.can-see-friendly-invisibles` | false | Team option. |
| `playerlist-objective.enabled` | true | Number column. |
| `playerlist-objective.value` | `%ping%` | Value. |
| `playerlist-objective.fancy-value` | `&7Ping: %ping%` | Text shown (1.20.3+). |
| `playerlist-objective.render-type` | `INTEGER` | INTEGER or HEARTS. |
| `belowname-objective.enabled` | false | Under-name text. |
| `belowname-objective.value`, `fancy-value`, `fancy-value-default`, `title` | `%health%`, `&c%health%`, `NPC`, `&cHealth` | Content. `fancy-value-default` is shown for entities without a value (NPCs). |
| `belowname-objective.view-distance` | 10 | Blocks. |
| `prevent-spectator-effect.enabled` | false | Stops spectators showing as grey names. |
| `bossbar.enabled`, `toggle-command`, `remember-toggle-choice`, `hidden-by-default` | false, `/bossbar`, false, false | Boss bar feature. |
| `bossbar.bars.ServerInfo` | PROGRESS, color `%animation:barcolors%`, progress `100`, text `&fWebsite: &bwww.domain.com` | Example bar. |
| `scoreboard.enabled`, `toggle-command`, `remember-toggle-choice`, `hidden-by-default`, `delay-on-join-milliseconds` | false, `/sb`, false, false, 0 | Sidebar feature. Two example boards (`scoreboard-1.20.3+`, `scoreboard`). |
| `layout.enabled` | false | Fixed layout (80 slots, example website/TeamSpeak/store lines). |
| `ping-spoof.enabled/value` | false / 0 | Fake ping. |
| `global-playerlist.*`, `per-world-playerlist.*` | false | Multi-server / per-world lists. Not needed. |
| `placeholders.date-format`, `time-format`, `time-offset`, `register-tab-expansion`, `locale` | `dd.MM.yyyy`, `[HH:mm:ss / h:mm a]`, 0, false, `en-US` | `%date%`/`%time%` and PAPI expansion. |
| `placeholder-output-replacements` | `%essentials_vanished%`: yes to `&7\| Vanished` | Needs PlaceholderAPI with the Essentials expansion (PlaceholderAPI is not installed). |
| `conditions.nick` | `%player%=%essentials_nickname%` | Example condition; also needs PAPI. |
| `placeholder-refresh-intervals.default-refresh-interval` | 500 (ms) | Update speed. Per-placeholder overrides listed. |
| `assign-groups-by-permissions` | false | Use `tab.group.<name>` instead of the permission plugin's primary group. |
| `primary-group-finding-list` | `Owner, Admin, Mod, Helper, default` | Order when assigning by permissions. |
| `permission-refresh-interval` | 1000 | ms. |
| `use-bukkit-permissions-manager`, `use-online-uuid-in-tablist` | false, true | Permission lookup / UUID use. |
| `components.minimessage-support`, `disable-shadow-for-heads` | true, true | MiniMessage tags and head rendering. |
| `compensate-for-packetevents-bug` | false | Workaround if packetevents is present and tab list misbehaves. |
| `proxy-support.enabled` | true | Proxy messaging. No proxy here; harmless, set false to be tidy. |
| `mysql.*` | disabled | Shared storage. |
| `debug` | false | Verbose log. |
| `config-version` | 7 | Do not edit. |

### groups.yml / users.yml / animations.yml / messages.yml

- `groups.yml`: `_DEFAULT_` has `tabprefix`/`tagprefix` = `%luckperms-prefix%`, `tabsuffix`/`tagsuffix` = `%luckperms-suffix%`, `customtabname: "%player%"`. A sample group `Player` sets coloured prefixes (`&0&l[&7&lPlayer&0&l] &3`). Delete the sample unless you name your LuckPerms group `Player`. A `per-world` example exists.
- `users.yml`: ships with developer examples (`_NEZNAMY_` and a UUID with suffix ` &b&lVerified`). **Delete both.**
- `animations.yml`: `MyAnimation1`, `ServerName`, `web`, `vote`, `Welcome`, `time`, `barcolors`. Used by the header/footer defaults (`%animation:Welcome%`, `%animation:time%`, `%animation:web%`).
- `messages.yml`: all command texts (`reload-success`, `no-permission`, toggles, nametag texts).

Properties per group/user: `tabprefix`, `tabsuffix`, `customtabname`, `tagprefix`, `tagsuffix`, `customtagname`, `abovename`, `belowname`, `header`, `footer`, `bossbar`, `scoreboard-name`.

## Interactions with DwellerMC

- **DwellerCore mob names:** TAB's name tag feature (`scoreboard-teams`) only creates scoreboard teams for **players**. DwellerCore does not use scoreboards or teams; it sets `customName` on mobs (visible within 12 blocks via `setCustomNameVisible`). They do not overlap. `belowname-objective` also targets players only.
- **Elite boss bars:** DwellerCore elites use Adventure `BossBar`s shown to nearby players. TAB's own boss bar feature is off. If you turn it on, the TAB bar and elite bars stack at the top of the screen; with several elites nearby the screen fills up. Keep TAB's bossbar disabled, or show it with a `display-condition` only at spawn.
- **Depth HUD:** DwellerCore's HUD is an **action bar** (`Depth N · Area Lv N`). TAB does not write the action bar, so no conflict. (LibsDisguises' notify bar *does*, see [Disguises](disguises.md).)
- **Sidebar:** DwellerCore uses no sidebar. TAB's scoreboard is free to use; unused by default. Danger: LibsDisguises can modify scoreboards (`Scoreboard.ModifyScoreboards`), see Disguises.
- **LibsDisguises:** TAB soft-depends on it (disguise-aware tab list). LD's `HideDisguisedPlayersFromTab` and `ShowPlayerDisguisesInTab` change the list; keep both at defaults.
- **Citizens:** Citizens NPCs of type player are tab-list entries unless `npc.tablist.disable` is true (default true: removed after 2 ticks). TAB's `scoreboard-teams` sorting can place NPC entries in teams; if NPC name tags look wrong, set `belowname-objective.fancy-value-default` (already `NPC`) and see [Citizens](citizens.md).
- **Chat:** TAB does not format chat. Use EssentialsChat (default `<{DISPLAYNAME}> {MESSAGE}`) or DiscordSRV. **Recommended owner of chat format: EssentialsChat**, with the LuckPerms prefix added via `{PREFIX}` once Vault is installed. TAB's `tagprefix` only affects the name above the head and the tab list, not chat.
- **EssentialsX:** with `change-displayname: true` Essentials changes display names; TAB's `%player%` is the real name, use `%condition:nick%` (needs PlaceholderAPI) to show nicknames.
- **LuckPerms:** `%luckperms-prefix%`, `%luckperms-suffix%`, `%group%` are built into TAB. With no groups or prefixes configured, the prefixes are empty. Create groups, then name them in `sorting-types`.
- **PlaceholderAPI:** not installed. TAB works without it for its own placeholders; any `%essentials_...%`, `%vault_...%` etc. will not resolve.

## Recommended settings

1. Switch to the correct jar first.
2. Edit the header/footer (replace "Server name", `www.domain.com`, the `<#FFFFFF>&m...` rule is fine); remove the `world-test` design.
3. Delete sample `users.yml` entries and the sample `Player` group.
4. Update `sorting-types` to your LuckPerms group names.
5. Keep `bossbar` and `scoreboard` disabled (elite bars already use the top of the screen).
6. `scoreboard-teams.enable-collision: false` if you do not want players pushing each other in tunnels (gameplay choice).

## Problems

| Symptom | Cause / fix |
|---|---|
| Log says wrong jar, tab list unchanged | Replace jar (see top). |
| Tab list shows demo header | Edit `header-footer.designs.default`. |
| Raw `%placeholder%` in output | The placeholder needs PlaceholderAPI or the plugin that provides it. Test with `/tab parse <player> <placeholder>`. |
| Names in wrong order | `sorting-types` group names do not match LuckPerms groups. |
| Tab list odd after LibsDisguises disguise | See LD `Scoreboard.*` and `HideDisguisedPlayersFromTab`. |

See also [Disguises](disguises.md), [Citizens](citizens.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [TAB wiki](https://github.com/NEZNAMY/TAB/wiki)
- [TAB releases](https://github.com/NEZNAMY/TAB/releases)

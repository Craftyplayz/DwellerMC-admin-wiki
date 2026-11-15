# Plan (Player Analytics)

**Version 5.8 build 3638.** Records player sessions, playtime, activity, joins, kills, deaths, TPS, performance and more, and shows them on a built-in web page and with `/plan` commands. Player-facing summary: [Stats](https://craftyplayz.github.io/DwellerMC-wiki/plugins/stats/).

A newer release (5.9, build 3867) was announced in the startup log. Updating is a straight jar swap; Plan patches its own database.

## Setup the owner must do

1. **Web port.** Plan starts its own web server on **port 8804** (`Webserver.Port`). To reach it from outside the machine, open or allocate TCP 8804 on the host (on a panel such as Pterodactyl, add a second allocation). Test at `http://<server address>:8804`.
2. **Login / HTTPS.** With no certificate the log says `User Authorization Disabled! (Not secure over HTTP)`. That means **anyone who can reach the port sees every page without logging in**: all player names, join addresses, sessions and so on. Pick one:
    - Keep the port closed to the internet and use it only locally or through an SSH tunnel.
    - Put a reverse proxy with HTTPS in front (nginx, Caddy) and set `Webserver.External_Webserver_address` (and `Use_X-Forwarded-For_Header: true` if it forwards real IPs). With a working HTTPS address Plan enables logins.
    - Or give Plan a certificate (`Webserver.Security.SSL_certificate.*`, a Java keystore `Cert.jks`).
3. **Create the first web user** in game or console once login is on: `/plan register`. Then `/plan setgroup <user> admin`. Which groups a player may pick when registering is controlled by the permission `plan.webgroup.<group>`.
4. **Geolocation** is off until you accept the GeoLite2 licence (`Data_gathering.Accept_GeoLite2_EULA: true`). The log currently shows `Failed to enable geolocation`. Leave it off if you do not want IP-based country data.
5. Set `Server.ServerName` (default `Plan`) to `DwellerMC`.

## Commands

All start with `/plan` (`/plan` alone shows the help).

| Command | Permission | Default | Effect |
|---|---|---|---|
| `/plan` | `plan.command` | everyone | Help / subcommand list. |
| `/plan inspect [player]` | `plan.player.self`, `plan.player.other` | self: everyone; other: op | Link to a player's page. |
| `/plan qinspect [player]` | same | same | Short in-game summary of a player. |
| `/plan ingame [player]` | `plan.ingame.self`, `plan.ingame.other` | self: everyone; other: op | Same summary in game. |
| `/plan search <text>` | `plan.search` | op | Find players by name. |
| `/plan players` | `plan.users` / players page | op | Link to the players page. |
| `/plan server`, `/plan servers`, `/plan network` | `plan.server`, `plan.servers`, `plan.network` | op | Links to server pages. |
| `/plan register [user]` | `plan.register.self`, `plan.register.other` | self: everyone; other: op | Create a web login. |
| `/plan unregister [user]` | `plan.unregister.self`, `plan.unregister.other` | self: everyone; other: op | Delete a web login. |
| `/plan logout <user>` | `plan.logout.other` | op | Log a web user out. |
| `/plan setgroup <user> <group>` | web group management | op | Change a web user's group. |
| `/plan json [player]` | `plan.json.self`, `plan.json.other` | self: everyone; other: op | Raw JSON of a player. |
| `/plan info` | `plan.info` | op | Plan and database status. |
| `/plan reload` | `plan.reload` | op | Reload config. |
| `/plan disable` | `plan.disable` | op | Disable Plan until restart. |
| `/plan db backup\|restore\|move\|hotswap\|clear\|remove ...` | `plan.data.backup`, `.restore`, `.move`, `.hotswap`, `.clear`, `.remove.player`, `.remove.server`, `.base` | op | Database maintenance. |
| `/plan export`, `/plan import` | `plan.data.export`, `plan.data.import` | op | Export/import data. |

Other permissions: `plan.data.merge` (op), `plan.ignore.commanduse` (false), `plan.ignore.afk` (false). Give `plan.ignore.afk` to a bot or staff account so idle time is not counted.

**LuckPerms default group:** players already get `plan.command`, `plan.player.self`, `plan.ingame.self`, `plan.register.self`, `plan.unregister.self` and `plan.json.self` by default. Nothing to add. If you do not want players to see their own stats or register web users, set those nodes to false on the `default` group. Give staff `plan.player.other`, `plan.search`, `plan.server` and the `plan.webgroup.admin` node as needed.

## Config reference (`plugins/Plan/config.yml`, 5.8 defaults)

### Server, plugin, database

| Key | Default | Effect |
|---|---|---|
| `Server.ServerName` | `Plan` | Name on the web page. |
| `Plugin.Logging.Locale` | `default` | Language. |
| `Plugin.Logging.Delete_logs_after_days` | 7 | Error log retention. |
| `Plugin.Logging.Create_new_locale_file_on_next_enable`, `Log_untranslated_locale_keys`, `Dev` | false | Developer options. |
| `Plugin.Update_notifications.Check_for_updates` / `Notify_about_DEV_releases` | true / false | Update messages. |
| `Plugin.Configuration.Allow_proxy_to_manage_settings` | true | Only matters behind a proxy. |
| `Database.Type` | `SQLite` | SQLite or MySQL. SQLite is fine for one server. |
| `Database.MySQL.*` | localhost, 3306, root, `minecraft`, db `Plan`, 8 connections | Only used with `Type: MySQL`. Never publish these values. |

### Webserver

| Key | Default | Effect |
|---|---|---|
| `Webserver.Port` | 8804 | Listening port. |
| `Webserver.Internal_IP` | `0.0.0.0` | Bind address. Set `127.0.0.1` for local-only (use with a reverse proxy). |
| `Webserver.Alternative_IP.Enabled/Address` | false, `your.domain.here:%port%` | Address shown in links when behind NAT. |
| `Webserver.External_Webserver_address` | `https://www.example.address` | Public address (reverse proxy). Set to yours. |
| `Webserver.Disable_Webserver` | false | Commands only, no web page. |
| `Webserver.Public_html_directory` | `public_html` | Extra static files. |
| `Webserver.Security.SSL_certificate.KeyStore_path/Key_pass/Store_pass/Alias` | `Cert.jks`, `default`, `default`, `alias` | HTTPS keystore. |
| `Webserver.Security.Disable_authentication` | false | Force no login (dangerous). |
| `Webserver.Security.Disable_registration` | false | Stops `/plan register`. |
| `Webserver.Security.CORS.Allow_origin` | `*` | Browser origin rule. |
| `Webserver.Security.Use_X-Forwarded-For_Header` | false | Trust the proxy's client IP. Only with a proxy. |
| `Webserver.Security.Access_log.Print_to_console` / `Remove_logs_after_days` | false / 30 | Web access log. |
| `Webserver.Security.IP_whitelist.Enabled/Whitelist` | false, `192.168.0.0`, `0:0:0:0:0:0:0:1` | Restrict web access by IP. Useful if you keep the port open. |
| `Webserver.Security.Cookies_expire_after` | 2 HOURS | Login length. |
| `Webserver.Cache.*` | 15 s, 7 d, 2 d, 5 min | Cache lifetimes. |

### Data gathering and time

| Key | Default | Effect |
|---|---|---|
| `Data_gathering.Geolocations` | true | Country lookup (needs the EULA flag). |
| `Data_gathering.Accept_GeoLite2_EULA` | false | Accept to download the database. |
| `Data_gathering.Geolocation_Download_URL` | `https://geodb.playeranalytics.net/GeoLite2-Country.mmdb` | Download source. |
| `Data_gathering.Ping` | true | Record ping. |
| `Data_gathering.Disk_space` | true | Record disk usage. |
| `Data_gathering.Commands.Log_unknown` / `Log_aliases_as_main_command` | false / true | Command usage stats. |
| `Data_gathering.Join_addresses.Enabled/Preserve_case/Preserve_invalid/Filter_out_from_data` | true, false, false, `play.example.com` | Which address players joined with. |
| `Time.Delays.Ping_server_enable_delay` | 300 s | Wait before measuring ping. |
| `Time.Delays.Ping_player_join_delay` | 30 s | Wait after join. |
| `Time.Delays.Wait_for_DB_Transactions_on_disable` | 20 s | Shutdown wait. |
| `Time.Thresholds.AFK_threshold` | 3 min | Idle time counted as AFK. |
| `Time.Thresholds.Activity_index.Playtime_threshold` | 30 min | Activity index input. |
| `Time.Thresholds.Remove_inactive_player_data_after` | 3650 d | Purge old players. |
| `Time.Thresholds.Remove_time_series_data_after` | 3650 d | Purge TPS/online history. |
| `Time.Thresholds.Remove_ping_data_after` | 14 d | Purge ping. |
| `Time.Thresholds.Remove_disabled_extension_data_after` | 2 d | Purge data of disabled extensions. |
| `Time.Periodic_tasks.*` | extension refresh 1 h (server) / 15 min (player), DB config check 1 min, DB clean 1 h | Background tasks. |

### Display, formatting, export, plugins

| Key | Default | Effect |
|---|---|---|
| `Display_options.Theme` / `Night_mode` | `default` / false | Look of the page. |
| `Display_options.Player_head_image_url` | `https://crafthead.net/helm/${playerUUIDNoDash}/120` | Player head images (external service). |
| `Display_options.Sessions.Order_world_pies_by_percentage` | false | Pie order. |
| `Display_options.Players_table.Show_on_server_page` / `Show_on_players_page` | 2500 / 25000 | Row limits. |
| `Display_options.Open_player_links_in_new_tab` | false | Links. |
| `Display_options.Graphs.Show_gaps_in_data` | false | Graph style. |
| `Display_options.Graphs.TPS.High_threshold/Medium_threshold` | 18 / 10 | Color bands. |
| `Display_options.Graphs.Disk_space.High_threshold/Medium_threshold` | 500 / 100 | MB bands. |
| `Display_options.Command_colors.Main/Secondary/Highlight` | `&2`, `&7`, `&f` | In-game command colors. |
| `Display_options.WorldPie` | 10 hex colors | World pie colors. |
| `Formatting.Decimal_points` | `#.##` | Number format. |
| `Formatting.Time_amount.*`, `Formatting.Dates.*` | e.g. `%days%d `, `MMM d YYYY, HH:mm:ss`, TimeZone `server` | Time/date text. |
| `World_aliases.List` / `Regex` | `world: world`; example regex | Rename worlds in charts. Add your dimension names. |
| `Export.*` | all parts false | Write static HTML/JSON to `Analysis Results` / `Raw JSON`. |
| `Plugins.Buycraft.Secret`, `Plugins.Factions.HideFactions`, `Plugins.Towny.HideTowns` | placeholders | For those plugins. Unused. |
| `Customized_files.Path` | `web` | Folder for custom web files. |

## Data files

`plugins/Plan/`: `config.yml`, `Cert.jks` (only if you add one), the SQLite database file, `locale.txt`, `logs/`, `public_html/`, `web/`. Back up the database file before updates. Do not share it: it holds names, UUIDs, join addresses and session history.

## Interactions with DwellerMC

- **No surface, no map.** Plan has no world map and needs none. Geolocation is the only map-like feature (countries of players); it is off until the EULA flag is set.
- **World pie:** Plan reports playtime per world. DwellerMC is one overworld, so the pie is one slice. Add an alias under `World_aliases.List` (for example `world: Dweller Caves`) if you want a nicer label.
- **Kills and deaths:** Plan counts player kills, mob kills and deaths. Leveled mobs from DwellerCore count as ordinary mob kills; levels are not shown. Deaths include deaths by elites.
- **Depth, levels, perks:** Plan does not know about them. A custom Plan extension would be needed to graph depth.
- **Extensions found at startup:** DiscordSRV, Essentials, EssentialsEco and LuckPerms show extra data on player pages (link status, nickname, group, balance). Extensions for TAB, Citizens or LibsDisguises do not exist in this install.
- **LuckPerms:** with groups later, the group appears on the player page.
- **Citizens:** player-type NPCs are not real players and are not recorded as sessions.
- **CoreProtect, Essentials:** both are listed as soft dependencies; they add their own data tabs when present.
- **Performance:** Plan samples TPS and entity/chunk counts; with deep generation at Y -2032 this is useful for watching chunk counts.

## Recommended settings

- Pick one access plan (closed port, IP whitelist, or HTTPS) before opening 8804.
- `Server.ServerName: DwellerMC`; add a world alias.
- Leave `Database.Type: SQLite`.
- Lower `Remove_inactive_player_data_after` if you want old players purged (default is ten years).
- Keep `Geolocations` false unless you want the country data.

## Problems

| Symptom | Cause / fix |
|---|---|
| Web page works but needs no login | HTTP with no certificate disables login. See setup step 2. |
| Link shows `localhost` | Set `Webserver.Alternative_IP` or `External_Webserver_address`. |
| `Failed to enable geolocation` | Accept the GeoLite2 EULA flag or set `Geolocations: false`. |
| `/plan register` says login is disabled | Authentication is off (no HTTPS). |
| `403 Forbidden` on a page | The web group lacks the matching `access.*` permission. |
| Nobody can edit groups | Run `/plan reload`; it restores the `admin` group if every group lost `manage.groups`. |

See also [DiscordSRV](discordsrv.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [Plan wiki](https://github.com/plan-player-analytics/Plan/wiki)
- [Web permissions](https://github.com/plan-player-analytics/Plan/wiki/Web-permissions)
- [Plan releases](https://github.com/plan-player-analytics/Plan/releases)

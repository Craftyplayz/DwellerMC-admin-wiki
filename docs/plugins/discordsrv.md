# DiscordSRV

**Version 1.30.5** (jar `DiscordSRV-Build-1.30.5.jar`). Bridges in-game chat, joins/leaves, deaths and advancements to a Discord server, lets Discord users chat into the game, can run console commands from Discord, link accounts and sync roles. Player-facing summary: [Discord bridge](https://craftyplayz.github.io/DwellerMC-wiki/plugins/discord/).

!!! warning "Current state on the test server"
    The log shows `No bot token has been set in the config; a bot token is required to connect to Discord.` and DiscordSRV then **disables itself**. Nothing works until you do the setup below.

## What it does

- Minecraft chat to a Discord channel and back (`DiscordChatChannel*` keys).
- Join, first join, leave, death and advancement messages as Discord embeds (`messages.yml`).
- Optional console channel (live log + run commands with `!c`).
- Account linking (`/discord link`), "Linked" role, group-to-role sync, nickname sync, ban sync (`synchronization.yml`).
- Optional "require linked account to play" (`linking.yml`) and Discord voice channels by proximity (`voice.yml`).
- Alerts on game events (`alerts.yml`).
- Plan has a built-in DiscordSRV extension (the Plan log shows `Registered extension: DiscordSRV`).

## Setup (owner must do)

1. Open the Discord Developer Portal and create a **New Application**.
2. **Installation** tab: turn off *User Install*, set *Install Link* to **None**.
3. **Bot** tab: enable **SERVER MEMBERS INTENT** and **MESSAGE CONTENT INTENT**. Leave *Public Bot* off.
4. **Reset Token**, copy it. Treat it like a password: never post it, never commit it, never put it on a wiki. If it leaks, reset it at once.
5. Copy the **Application ID**, open `https://scarsz.me/authorize`, paste the ID, pick your server, Authorize.
6. In Discord enable *Developer Mode* (User Settings, Advanced). Right-click the chat channel and **Copy ID**.
7. Edit `plugins/DiscordSRV/config.yml`: set `BotToken`, and `Channels: {"global": "<channel id>"}`. Optional: `DiscordConsoleChannelId`.
8. Give the bot a role (Administrator, or at least: View Channel, Send Messages, Manage Messages, Embed Links, Read Message History, Add Reactions; plus Manage Roles / Manage Nicknames / Ban Members / Manage Channels only for the sync features you turn on). The bot role must sit **above** any role it should sync.
9. Restart (or `/discord reload`) and watch the log for the bot connecting.

The server needs outbound HTTPS to Discord. Do not put the token in any file that is committed to git.

## Commands

| Command | Permission | Default | Effect |
|---|---|---|---|
| `/discord` (alias `/discordsrv`) | `discordsrv.discord` | everyone | Shows the invite link (`DiscordInviteLink`). |
| `/discord help` | `discordsrv.help` | everyone | Command list. |
| `/discord link` | `discordsrv.link` | everyone | Gives a 4-digit code to DM to the bot. |
| `/discord linked` | `discordsrv.linked` | everyone | Shows your link status. |
| `/discord unlink` | `discordsrv.unlink` | op | Removes your link. |
| `/discord broadcast <msg>` | `discordsrv.bcast` | op | Sends a message to the main channel. |
| `/discord reload` | `discordsrv.reload` | op | Reloads configs. |
| `/discord debug`, `/discord debugger` | `discordsrv.debug` | op | Debug report (uploads to an external paste service; do not share publicly). |
| `/discord resync` | `discordsrv.resync` | op | Force group/role resync. |
| `/discord language` | `discordsrv.language` | op | Change plugin language. |

Discord side: `!c <command>` (`DiscordChatChannelConsoleCommandPrefix`), `playerlist` (online list), and the canned responses.

## Permissions

| Permission | Default | Effect |
|---|---|---|
| `discordsrv.player` | true | Parent: chat, help, link, linked, discord, nicknamesync. |
| `discordsrv.admin` | op | Parent of all admin nodes plus player. |
| `discordsrv.chat` | true | Chat is forwarded to Discord. Deny to hide someone from the bridge. |
| `discordsrv.silentjoin` / `silentquit` | false | No join/leave message in Discord. Give to staff who vanish. |
| `discordsrv.nicknamesync` | true | Included in nickname sync. |
| `discordsrv.updatenotification` | op | Update message on join. |
| `discordsrv.link.others`, `linked.others`, `unlink.others` | op | Manage other people's links. |
| `discordsrv.groupsyncwithcommands` | op | Trigger group sync via permission-plugin commands. |

**LuckPerms (fresh, no groups):** all player nodes default to true through Bukkit defaults, so the `default` group needs no grants. Optionally give your staff group `discordsrv.silentjoin` and `discordsrv.silentquit`.

## Config files and keys

Generated in `plugins/DiscordSRV/` on first successful start: `config.yml`, `messages.yml`, `linking.yml`, `synchronization.yml`, `voice.yml`, `alerts.yml`. Defaults below are for 1.30.5.

### config.yml: connection and general

| Key | Default | Effect |
|---|---|---|
| `BotToken` | `"BOTTOKEN"` | Bot token. Required. |
| `Channels` | `{"global": "000000000000000000"}` | In-game channel name to Discord channel ID. `global` is the main chat. |
| `DiscordConsoleChannelId` | `"000000000000000000"` | Console channel (zeros = off). |
| `DiscordInviteLink` | `https://discord.gg/changethisintheconfig.yml` | Shown by `/discord`. Change it. |
| `DiscordGameStatus` | `["playing Minecraft"]` | Bot status text. |
| `DiscordOnlineStatus` | `ONLINE` | ONLINE / IDLE / DND / INVISIBLE. |
| `StatusUpdateRateInMinutes` | 2 | How often the status rotates. |
| `AvatarUrl` | `""` | Avatar override for webhook messages. |
| `ForcedLanguage` | `none` | Plugin language. |
| `ForceTLSv12`, `NoopHostnameVerifier` | true, false | TLS options. Leave. |
| `MaximumAttemptsForSystemDNSBeforeUsingFallbackDNS` | 3 | DNS fallback. |
| `TimestampFormat`, `DateFormat`, `Timezone` | `EEE, d. MMM yyyy HH:mm:ss z`, `yyyy-MM-dd`, `default` | Formats. |
| `MinecraftMentionSound` | true | Ping sound when mentioned from Discord. |
| `DisabledPluginHooks` | `[]` | Plugins to not hook. |
| `UseModernPaperChatEvent` | false | Use Paper's newer chat event. Try true if chat is not forwarded (see Problems). |
| `EnablePresenceInformation` | false | Needs the presence intent. Leave off. |
| `CancelConsoleCommandIfLoggingFailed` | true | Safety for the console command log. |
| `Debug` | `[]` | Debug categories. |
| `ProxyHost/Port/User/Password` | placeholders | HTTP proxy for Discord traffic. Unused unless set. |

### config.yml: chat channel

| Key | Default | Effect |
|---|---|---|
| `DiscordChatChannelDiscordToMinecraft` | true | Discord messages show in game. |
| `DiscordChatChannelMinecraftToDiscord` | true | In-game chat goes to Discord. |
| `DiscordChatChannelTruncateLength` | 256 | Max forwarded characters. |
| `DiscordChatChannelTranslateMentions` | true | Convert @mentions. |
| `DiscordChatChannelAllowedMentions` | `[user, channel, emote]` | Which mentions work. Keep `everyone`/`role` out. |
| `DiscordChatChannelEmojiBehavior`, `...EmoteBehavior` | `name` | Show emoji as name. |
| `DiscordChatChannelPrefixRequiredToProcessMessage` | `""` | Only forward messages with this prefix. |
| `DiscordChatChannelPrefixActsAsBlacklist` | false | Invert the prefix rule. |
| `DiscordChatChannelRolesAllowedToUseColorCodesInChat` | `["Developer","Owner","Admin","Moderator"]` | Discord roles that may use color codes. |
| `DiscordChatChannelBroadcastDiscordMessagesToConsole` | true | Log Discord chat to console. |
| `DiscordChatChannelRequireLinkedAccount` | false | Only linked Discord users may talk in game. |
| `DiscordChatChannelBlockBots` | false | Ignore other bots. |
| `DiscordChatChannelBlockWebhooks` | true | Ignore webhooks. |
| `DiscordChatChannelBlockedIds` | three zero IDs | Discord user IDs to ignore. |
| `DiscordChatChannelBlockedRolesIds`, `...BlockedRolesAsWhitelist` | zero IDs, false | Role-based block/allow. |
| `DiscordChatChannelRolesSelection`, `...AsWhitelist`, `...RoleAliases` | examples | Which role shows as the prefix, with aliases (`{"Developer":"Dev"}`). |
| `DiscordChatChannelGameFilters`, `...DiscordFilters` | `{}`, two list-command regexes | Regex filters (match replaced by the value). |
| `DiscordChatChannelListCommandEnabled/Message/Expiration` | true, `playerlist`, 10 | Discord `playerlist` command. |
| `DiscordCannedResponses` | `!ip`, `!site` examples | Bot replies. Replace or empty. |

### config.yml: console channel and remote commands

| Key | Default | Effect |
|---|---|---|
| `DiscordConsoleChannelLogRefreshRateInSeconds` | 5 | Log batch interval. |
| `DiscordConsoleChannelUsageLog` | `Console-%date%.log` | Log of commands run from Discord. |
| `DiscordConsoleChannelBlacklistedCommands` | `["?","op","deop","execute"]` | Commands blocked from the console channel. |
| `DiscordConsoleChannelFilters` | two regexes | Lines hidden from Discord. |
| `DiscordConsoleChannelLevels` | `[info, warn, error]` | Log levels sent. |
| `DiscordConsoleChannelUseCodeBlocks`, `...BlockBots` | true, true | Formatting / ignore bots. |
| `DiscordChatChannelConsoleCommandEnabled` | true | `!c` in the chat channel. **Consider false.** |
| `...ConsoleCommandPrefix` | `!c` | Prefix. |
| `...ConsoleCommandRolesAllowed` | `["Owner","Developer"]` | Discord roles allowed. Matched by **name**: anyone who can create a role named "Owner" gets console access. Lock down role creation. |
| `...ConsoleCommandWhitelist`, `...BypassRoles`, `...ActsAsBlacklist` | `["say","lag","tps"]`, `["Owner","Developer"]`, false | Allowed commands. |
| `...ConsoleCommandExpiration`, `...ExpirationDeleteRequest` | 0, true | Auto-delete replies. |

### config.yml: channel updater, linking, watchdog

| Key | Default | Effect |
|---|---|---|
| `ChannelTopicUpdaterChannelTopicsAtShutdownEnabled` | true | Sets "offline" topic on stop. |
| `ChannelTopicUpdaterRateInMinutes` | 10 | Topic refresh. |
| `ChannelUpdater` | two example entries (ID `0000000000000000`) | Channel renamed to `%playercount% players online` / `Current TPS: %tps%`. Remove or set IDs. |
| `MinecraftDiscordAccountLinkedRoleNameToAddUserTo` | `Linked` | Role given on link (create it, below the bot role). |
| `MinecraftDiscordAccountLinkedConsoleCommands`, `...UnlinkedConsoleCommands` | `["","",""]` | Console commands run on link/unlink. |
| `MinecraftDiscordAccountLinkedAllowRelinkBySendingANewCode` | false | Re-link allowed. |
| `MinecraftDiscordAccountLinkedUsePM` | true | Link codes by DM. |
| `ServerWatchdogEnabled/Timeout/MessageCount` | true, 30, 3 | Pings the guild owner if the server stops ticking for 30 s. |
| `Experiment_Webhook*` | false | Webhook chat delivery (player skins as avatars). Keep webhook URLs private. |
| `Experiment_Jdbc*` | placeholders | MySQL account-link backend (off unless filled). |
| `Experiment_MCDiscordReserializer_*` | false | Experimental formatting converters. |

### messages.yml (key formats)

| Key | Default |
|---|---|
| `MinecraftChatToDiscordMessageFormat` | `**%primarygroup%** %displayname% » %message%` |
| `MinecraftChatToDiscordMessageFormatNoPrimaryGroup` | `%displayname% » %message%` |
| `DiscordToMinecraftChatMessageFormat` | `[<aqua>Discord</aqua> \| %toprolecolor%%toprolealias%<reset>] %name%%reply% » %message%` |
| `DiscordToMinecraftChatMessageFormatNoRole` | `[<aqua>Discord</aqua>] %name%%reply% » %message%` |
| `MinecraftPlayerJoinMessage` / `FirstJoin` / `Leave` / `Death` / `Achievement` | Embeds, enabled; colors green / gold / red / black / gold. Death text is `%deathmessage%`. |
| `DiscordChatChannelServerStartupMessage`, `...ShutdownMessage` | `:white_check_mark: **Server has started**` / `:octagonal_sign: **Server has stopped**` |
| `DiscordCommandFormat` | `&bJoin us on Discord at {INVITE}&b. ...` |
| `ChannelTopicUpdater*Format` | Chat topic: players online, uniques, uptime. Console topic: TPS, memory, version. |
| Link messages | `CodeGenerated`, `DiscordAccountLinked`, `MinecraftAccountLinked`, `LinkedCommandSuccess`, etc. |

Each event message also has `Enabled`, `Webhook{Enable,AvatarUrl,Name}`, `Content`, and an `Embed` block (color, author, title, fields, footer, timestamp).

### linking.yml, synchronization.yml, voice.yml, alerts.yml

| File | Key | Default | Effect |
|---|---|---|---|
| linking | `Require linked account to play.Enabled` | false | Kick unlinked players on login. Do not enable unless every player can reach Discord. |
| linking | `Bypass names`, `Whitelisted players bypass check`, `Check banned players` | `[Scarsz, Vankka]`, true, false | Exceptions. Replace the example names. |
| linking | `Must be in Discord server`, `Subscriber role.*` | true, off | Extra gates. |
| synchronization | `NicknameSynchronizationEnabled` | false | Discord nickname follows the in-game name. |
| synchronization | `GroupRoleSynchronizationGroupsAndRolesToSync` | `{"trusted": 0..., "vip": 0...}` | LuckPerms group to Discord role ID. Example IDs; replace. |
| synchronization | `...MinecraftIsAuthoritative`, `...OneWay`, `...OnLink`, `...CycleTime` | true, false, true, 5 | Sync direction and interval (minutes). |
| synchronization | `BanSynchronizationDiscordToMinecraft`, `...MinecraftToDiscord` | false, false | Ban sync. |
| voice | `Voice enabled` | false | Proximity voice channels. |
| alerts | `Alerts` | empty | Event-triggered messages. |

## Data files

`plugins/DiscordSRV/`: configs above, `accounts.aof` (the link table; do not share, it maps Discord IDs to UUIDs), `discordsrv.log`, `Console-<date>.log`, `debug/`, `lang/`. No database unless `Experiment_Jdbc*` is set.

## Interactions with other DwellerMC plugins

- **DwellerCore:** mob names and elite boss bars are not chat and are not forwarded. The depth HUD is an action bar, not forwarded. A player killed by a leveled mob produces a death message containing the mob's custom name, which goes to Discord as `%deathmessage%`.
- **EssentialsX / EssentialsChat:** DiscordSRV soft-depends on Essentials (vanished players, nicknames). The in-game format is set by EssentialsChat (default `<{DISPLAYNAME}> {MESSAGE}`). Let **EssentialsChat own the in-game chat format**; DiscordSRV only owns the Discord side. Do not also format chat from TAB.
- **LuckPerms:** `%primarygroup%` and role sync need a permission hook. There are no groups yet, so the group reads as `default`. Create groups before using role sync. Essentials logs that Vault is not installed, so `{PREFIX}`/`{SUFFIX}` in Essentials chat do not work either.
- **BlockReports:** strips chat signatures; DiscordSRV still gets the chat event. If chat is not forwarded, try `UseModernPaperChatEvent: true`.
- **TAB:** TAB does not touch chat. No conflict.
- **Plan:** Plan's DiscordSRV extension adds link info to Plan pages.
- **LibsDisguises:** disguised players chat normally.

## Recommended settings

- `DiscordChatChannelConsoleCommandEnabled: false` unless you need it; keep the whitelist short.
- Replace all example role names / IDs; empty `ChannelUpdater` and `DiscordCannedResponses` if unused.
- Set `DiscordInviteLink`.
- Do not enable `Require linked account to play` on a small community server.

## Problems

| Symptom | Cause / fix |
|---|---|
| `No bot token has been set` and plugin disables | Set `BotToken`. |
| `Disallowed intents` | Enable SERVER MEMBERS and MESSAGE CONTENT intents in the portal. |
| Bot online, nothing in Discord | Wrong channel ID, or the bot lacks View/Send on that channel. |
| Discord messages reach the game but game chat does not reach Discord | Another plugin cancels chat first, or try `UseModernPaperChatEvent: true`. |
| Role sync does nothing | Bot role below target role; group names do not match LuckPerms groups. |

See also [Plan](plan.md), [BlockReports](blockreports.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [DiscordSRV docs](https://docs.discordsrv.com/)
- [Initial setup](https://docs.discordsrv.com/installation/initial-setup/)

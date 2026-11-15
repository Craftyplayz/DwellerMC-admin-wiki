# BlockReports

**Version 2.2** by AlphaAlex115. Stops Minecraft's chat reporting system from working on the server: it strips chat signatures, hides the "chat messages can't be verified" popup, blocks the signing session and prevents the kicks that can follow. It also adds `/chat`, `/msg`, `/r` and `/me` so players whose accounts are blocked from chatting client-side (for example some UK accounts) can still talk. Player-facing summary: [Chat reporting](https://craftyplayz.github.io/DwellerMC-wiki/plugins/chat-reporting/).

## What it changes on startup

On enable it sets `enforce-secure-profile=false` in `server.properties` by itself (the log says `Changed enforce-secure-profile from true to false in server properties`). The change takes effect for new connections; restart once if players still see signature errors.

## Commands

| Command | Aliases | Permission | Default | Effect |
|---|---|---|---|---|
| `/blockreports reload` | `/br` | `blockreports.admin` | op | Reload `config.yml`. |
| `/chat <message>` | `/c` | `blockreports.chat` | everyone | Send a normal public message by command. |
| `/msg <player> <message>` | `/tell`, `/w`, `/t` | `blockreports.message` | everyone | Private message. |
| `/r <message>` | `/reply` | `blockreports.message` | everyone | Reply to the last private message. |
| `/me <action>` | none | `blockreports.me` | everyone | Emote message. |

Players need no LuckPerms grants; all four player permissions default to true.

## Config (`plugins/BlockReports/config.yml`)

| Key | Default | Effect |
|---|---|---|
| `strip-server-signatures` | true | Outgoing chat is sent unsigned (as system-style messages), so it cannot be reported. |
| `hide-secure-chat-warning` | true | Removes the client warning toast about unverifiable chat. |
| `neutralise-chat-sessions` | true | Replaces the public key in incoming chat-session packets with null so no signing session forms. |
| `prevent-chat-kicks` | true | Avoids "invalid signature" and chat-validation kicks. |
| `enable-chat-command` | true | Provides `/chat` and `/c`. |
| `chat-command-hint` | true | Tells players about `/chat` on join until their first message. |
| `enable-logging` | false | Verbose debug logging. |

## Interactions

- **EssentialsX uses the same commands.** Essentials also registers `/msg`, `/tell`, `/w`, `/r`, `/reply` and `/me`. Bukkit hands each label to one plugin, so only one of the two answers. Test each once after startup. If Essentials answers, you keep social spy, `/ignore` and Essentials' message formats; chat signatures are still stripped by BlockReports' packet handling either way.
- **Chat format:** BlockReports does not format chat. Format comes from EssentialsChat.
- **DiscordSRV:** still receives chat events. See [DiscordSRV](discordsrv.md).
- **TAB:** no overlap.
- **Packet handling:** BlockReports changes chat packets itself. It works alongside packetevents (used by LibsDisguises); no known conflict.
- **DwellerCore:** unrelated (the depth HUD and mob names are not chat).

## Recommended settings

Keep every default. If you want players to keep using Essentials' `/msg` and `/me`, test once after startup that each label does what you expect.

## Problems

| Symptom | Cause / fix |
|---|---|
| Players still get a "chat can't be verified" toast | Restart once so `enforce-secure-profile=false` is read; confirm `hide-secure-chat-warning: true`. |
| Kicked with chat validation errors | Set `prevent-chat-kicks: true`, update the plugin if the client is newer than 26.3. |
| `/msg` behaves like Essentials, not BlockReports | Essentials owns that label. Expected; both achieve unsigned messages. |
| Player cannot type in chat at all | Client-side block on their account. They use `/chat <message>`. |

See also [DiscordSRV](discordsrv.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [BlockReports on Modrinth](https://modrinth.com/plugin/blockreports)

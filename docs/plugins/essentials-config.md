# EssentialsX config reference

File: `plugins/Essentials/config.yml`. Back to [EssentialsX](essentials.md). Apply changes with `/essentials reload`, except keys marked **restart**.

The server config equals the default except `disabled-commands: [top, tpr, settpr]`. Use "none" or empty values exactly as shown. Keys of EssentialsXProtect (`protect:`) are ignored because that module is not installed.

## Names and display

| Key | Default | Effect |
|---|---|---|
| `ops-name-color` | `'4'` | Color of op names. `none` disables |
| `nickname-prefix` | `'~'` | Prefix shown before nicknames. Hidden with `essentials.nick.hideprefix` |
| `max-nick-length` | `16` | Longest nickname |
| `allowed-nicks-regex` | `^[a-zA-Z_0-9§]+$` | Pattern a nickname must match. Bypass: `essentials.nick.allowunsafe` |
| `nick-blacklist` | empty | Banned nickname phrases (regex allowed). Bypass: `essentials.nick.blacklist.bypass` |
| `ignore-colors-in-max-nick-length` | `false` | Don't count color codes in the length |
| `reset-nick-on-name-change` | `false` | Clear the nickname when the account is renamed |
| `hide-displayname-in-vanish` | `true` | Hide display names of vanished players |
| `change-displayname` | `true` | Let Essentials set display names. Turn off if another plugin does it |
| `change-tab-complete-name` | `false` | Tab complete display names |
| `message-colors.primary` / `.secondary` | `#ffaa00` / `#ff5555` | Colors of Essentials messages |
| `per-player-locale` | `false` | Use each player's client language |
| `list` | `Admins: owner admin` | Groups shown in `/list`. Groups can be merged, capped (`name: 20`) or hidden (`name: hidden`) |
| `real-names-on-list` | `false` | Show real names next to nicknames in `/list` |
| `non-ess-in-help` | `true` | Show other plugins in `/help` |
| `hide-permissionless-help` | `true` | Hide plugins the player has no access to |
| `debug` | `false` | Extra console output |

## Commands

| Key | Default | Effect |
|---|---|---|
| `disabled-commands` | `[top, tpr, settpr]` | Commands Essentials will not run. Matches the exact word typed, so aliases need their own entry |
| `overridden-commands` | empty | Commands Essentials keeps even if another plugin has them |
| `verbose-command-usages` | `true` | Long usage messages |
| `command-cooldowns` | empty | Seconds per command. Wildcards and regex (start with `^`) allowed |
| `command-cooldown-persistence` | `true` | Keep cooldowns over restarts |
| `command-costs` | empty | Money cost per use, e.g. `kit-tools: 1500` |
| `mute-commands` | `[f, kittycannon]` | Commands blocked while muted. `'*'` blocks all |
| `socialspy-commands` | msg, mail, me and aliases | Commands shown to SocialSpy. `'*'` for all |
| `socialspy-listen-muted-players` | `true` | Show muted players' messages to spies |
| `socialspy-messages` | `true` | Spy on private messages as well |
| `socialspy-uses-displaynames` | `true` | Use display names in spy output |
| `player-commands` | list | Only used when `use-bukkit-permissions` is `false` |
| `use-bukkit-permissions` | `true` | Use the permission plugin (LuckPerms). Keep `true` |
| `log-command-block-commands` | `true` | Log command block commands |
| `log-console-commands` | `true` | Log console commands |

## Teleporting

| Key | Default | Effect |
|---|---|---|
| `teleport-safety` | `true` | Move players from unsafe targets to a nearby safe spot. `false` cancels the teleport instead |
| `force-disable-teleport-safety` | `false` | Skip all checks. Do not enable here |
| `force-safe-teleport-location` | `false` | Also check for creative, adventure and god mode players |
| `is-water-safe` | `false` | Treat water as safe |
| `teleport-passenger-dismount` | `true` | Dismount riders before teleporting |
| `teleport-cooldown` | `0` | Seconds between teleports |
| `teleport-delay` | `0` | Seconds to wait before a teleport. Moving or being hit cancels |
| `teleport-invulnerability` | `4` | Seconds of protection after a command teleport |
| `teleport-to-center` | `true` | Land on the block center |
| `register-back-in-listener` | `false` | Record `/back` locations for teleports from other plugins |
| `tpa-accept-cancellation` | `120` | Seconds before a `/tpa` expires. `0` never |
| `tpa-max-requests` | `5` | Pending requests per player |
| `world-teleport-permissions` | `false` | Require `essentials.worlds.<world>` for `/world`, `/back`, `/tp` |
| `world-change-fly-reset` | `true` | Reset flight on world change |
| `world-change-preserve-flying` | `true` | Keep flying on world change (needs `essentials.fly`) |
| `gamemode-change-preserve-flying` | `false` | Keep flying on game mode change |
| `world-change-speed-reset` | `true` | Reset speed on world change |
| `max-walk-speed`, `max-fly-speed` | `0.8` | Speed caps |
| `teleport-when-freed` | `back` | After jail: `back`, `spawn` or `off` |
| `jail-online-time` | `false` | Jail time counts only online time |

## Homes

| Key | Default | Effect |
|---|---|---|
| `sethome-multiple` | `default: 3`, `vip: 5`, `staff: 10` | Home limits per rank. See [Homes](essentials.md#homes) |
| `spawn-if-no-home` | `true` | `/home` with no homes goes to the **world spawn**. Set `false` here |
| `confirm-home-overwrite` | `false` | Ask before overwriting a home |
| `world-home-permissions` | `false` | Require `essentials.worlds.<world>` for `/home` |
| `compass-towards-home-perm` | `false` | Point the compass at the first home only with `essentials.home.compass` |
| `update-bed-at-daytime` | `true` | No effect on 1.15 and later |

## Spawn and new players (EssentialsXSpawn)

| Key | Default | Effect |
|---|---|---|
| `newbies.announce-format` | `&dWelcome {DISPLAYNAME}&d to the server!` | Broadcast on first join. `''` disables |
| `newbies.spawnpoint` | `newbies` | Spawn name used on first join. `none` uses the world spawn |
| `newbies.kit` | `tools` | Kit given on first join. Ignores permission, cost and delay. `''` disables |
| `respawn-listener-priority` | `high` | **restart**. Event priority for respawns. `none` turns Essentials respawn handling off. DwellerCore uses NORMAL, so `high` or `highest` overrides it |
| `spawn-join-listener-priority` | `high` | **restart**. Same, for joins |
| `respawn-at-home` | `false` | `true`: respawn at bed, else first home, else spawn. `false`: always the spawn |
| `respawn-at-home-bed` | `true` | Prefer the bed over homes. Needs `respawn-at-home: true` |
| `respawn-at-anchor` | `false` | Respect respawn anchors (only if `respawn-at-home` is on) |
| `random-spawn-location` | `none` | Random teleport spot for first joins. Needs `/settpr`. Keep `none` |
| `random-respawn-location` | `none` | Same for deaths. Keep `none` |
| `spawn-on-join` | `false` | `true`, or a list of groups, to teleport returning players to spawn on every join. `essentials.spawn-on-join.exempt` skips it |

## Chat (EssentialsXChat)

See [Chat](essentials.md#chat-essentialsxchat) for the `chat.*` keys.

## Economy

| Key | Default | Effect |
|---|---|---|
| `starting-balance` | `0` | New player balance |
| `currency-symbol` | `$` | Symbol |
| `currency-symbol-suffix` | `false` | Put the symbol after the number |
| `max-money` | `10000000000000` | Balance cap |
| `min-money` | `-10000` | Lowest balance. `0` turns loans off |
| `minimum-pay-amount` | `0.001` | Smallest `/pay` |
| `pay-excludes-ignore-list` | `false` | Block `/pay` to players who ignore you |
| `economy-log-enabled` | `false` | Log sells and trades |
| `economy-log-uuids` | `false` | Use UUIDs in that log |
| `economy-log-update-enabled` | `false` | Also log Vault transactions |
| `show-zero-baltop` | `true` | List balances of 0 or less |
| `baltop-requirements` | `minimum-balance: 0`, `minimum-playtime: 0` | Filters for `/baltop` (playtime in seconds) |
| `baltop-entry-limit` | `-1` | Cache size. `-1` unlimited |
| `npcs-in-balance-ranking` | `false` | Count NPC balances |
| `sell-multipliers` | `default: 1.0`, `double: 2.0`, `triple: 3.0` | `/sell` multipliers, given by `essentials.sell.multiplier.<name>` |
| `allow-bulk-buy-sell` | `true` | Sneak-click signs to buy or sell in bulk |
| `allow-selling-named-items` | `false` | Allow selling renamed items |
| `default-enabled-confirm-commands` | empty | Commands (`pay`, `clearinventory`) that ask for confirmation |

## Kits

| Key | Default | Effect |
|---|---|---|
| `skip-used-one-time-kits-from-kit-list` | `false` | Hide used one-time kits from `/kit` |
| `kit-auto-equip` | `false` | Put kit armor on if the armor slots are empty |
| `pastebin-createkit` | `false` | `/createkit` gives a link instead of writing `kits.yml` |
| `use-nbt-serialization-in-createkit` | `false` | Save kits as NBT. Can't be downgraded |
| `unsafe-enchantments` | `false` | Allow unsafe enchants in kits and items |
| `drop-items-if-full` | `false` | Drop items at the feet when the inventory is full |

## AFK

| Key | Default | Effect |
|---|---|---|
| `auto-afk` | `300` | Seconds until AFK (needs `essentials.afk.auto`). `-1` off |
| `auto-afk-timeout` | `-1` | Seconds AFK before kick or commands. `essentials.afk.kickexempt` skips |
| `afk-timeout-commands` | empty | Commands to run instead of a kick. Must remove or un-AFK the player |
| `freeze-afk-players` | `false` | Freeze and protect AFK players |
| `disable-item-pickup-while-afk` | `false` | AFK players can't pick up items |
| `cancel-afk-on-interact`, `-on-move`, `-on-chat`, `-on-fish` | `true` | What ends AFK |
| `sleep-ignores-afk-players` | `true` | AFK players don't block night skipping |
| `sleep-ignores-vanished-player` | `true` | Same for vanished players |
| `afk-list-name` | `none` | `/list` name while AFK |
| `broadcast-afk-message` | `true` | Tell everyone, not just the player |

## Messages, join and death

| Key | Default | Effect |
|---|---|---|
| `death-messages` | `true` | Show death messages |
| `send-info-after-death` | `false` | Tell players where they died |
| `vanishing-items-policy`, `binding-items-policy` | `keep` | For `essentials.keepinv` holders: `keep`, `drop`, `delete` |
| `allow-silent-join-quit` | `false` | Enable `essentials.silentjoin` and `essentials.silentquit` |
| `custom-join-message`, `custom-quit-message`, `custom-new-username-message` | `none` | Custom text. `""` hides it. Placeholders `{PLAYER}`, `{USERNAME}`, `{PREFIX}`, `{SUFFIX}`, `{ONLINE}`, `{UNIQUE}`, `{UPTIME}` |
| `hide-join-quit-messages-above` | `-1` | Hide join and quit messages above this player count |
| `use-custom-server-full-message`, `use-custom-whitelist-message` | `true` | Use Essentials' wording |
| `delay-motd` | `0` | Milliseconds before the join MOTD. `-1` off |
| `notify-no-new-mail` | `true` | Tell players when they have no mail |
| `notify-player-of-mail-cooldown` | `60` | Seconds between mail reminders |
| `mails-per-minute` | `1000` | Mail rate limit |
| `last-message-reply-recipient` | `true` | `/r` answers the person you wrote to last |
| `last-message-reply-recipient-timeout` | `180` | Seconds before that updates |
| `last-message-reply-vanished` | `false` | Allow replying to vanished players |
| `login-attack-delay` | `5` | Seconds after login before dealing damage |

## Items and misc

| Key | Default | Effect |
|---|---|---|
| `item-spawn-blacklist` | empty | Items `/item` and `/give` refuse |
| `permission-based-item-spawn` | `false` | Use permissions instead of the blacklist |
| `default-stack-size` | `-1` | Default `/item` amount. Below 1 means a full stack |
| `oversized-stacksize` | `64` | Oversized stack size (`essentials.oversizedstacks`) |
| `repair-enchanted` | `true` | `/repair` works on enchanted items |
| `spawnmob-limit` | `10` | Max mobs per `/spawnmob` |
| `tree-command-range-limit` | `300` | Max range for `/tree` and `/bigtree` |
| `max-itemlore-lines` | `10` | Lore lines |
| `max-projectile-speed` | `8` | `/fireball` speed |
| `warn-on-smite` | `true` | Tell people they were struck |
| `heal-cooldown` | `60` | Seconds between `/heal` and `/feed` |
| `remove-effects-on-heal` | `true` | `/heal` clears potion effects |
| `near-radius` | `200` | Default `/near` radius |
| `max-mute-time`, `max-tempban-time` | `-1` | Longest allowed, in seconds |
| `remove-god-on-disconnect` | `false` | Clear god mode on quit |
| `no-god-in-worlds` | empty | Worlds where god mode is off |
| `milk-bucket-easter-egg` | `true` | Milk makes mobs babies |
| `send-fly-enable-on-join` | `true` | Tell flying-capable players on join |
| `allow-direct-hat` | `true` | Click a helmet slot with an item to wear it |
| `world-time-permissions` | `false` | Per-world `/time` permission |
| `enabledSigns` | empty | Sign types. Empty means signs are off |
| `sign-use-per-second` | `4` | Sign click rate limit |
| `allow-old-id-signs` | `false` | Keep old item-ID signs working |
| `unprotected-sign-names` | empty | Sign names Essentials does not protect |
| `per-warp-permission` | `false` | Require `essentials.warps.<name>` |
| `backup.interval` | `30` | Minutes between backups. Does nothing without a `backup.command` |
| `backup.always-run` | `false` | Run with nobody online |
| `update-check` | `true` | Tell holders of `essentials.updatecheck` about new versions |
| `safe-usermap-names` | `true` | Clean names in the user map |
| `allow-world-in-broadcastworld` | `true` | Allow naming a world in `/broadcastworld` |

## Links

- [EssentialsX config](https://essentialsx.net/wiki/Home.html)
- [Module breakdown](https://essentialsx.net/wiki/Module-Breakdown.html)

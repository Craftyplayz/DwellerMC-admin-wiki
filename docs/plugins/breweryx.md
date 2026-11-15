# BreweryX

**Version 3.7.1.** Brewing and drinking plugin: ingredients are cooked in a cauldron, optionally distilled in a brewing stand, aged in wooden barrels, and then cause drunkenness when drunk. Player page: [BreweryX](https://craftyplayz.github.io/DwellerMC-wiki/plugins/breweryx/).

## How brewing works
1. **Cauldron.** Fill a cauldron with water, put a heat source under it (fire, soul fire, lava, magma block, campfire or soul campfire), right-click ingredients in, and wait the recipe's `cookingtime` in real minutes. A clock right-click shows the elapsed time (`brewery.cauldron.time`). Extract with glass bottles (`brewery.cauldron.fill`).
2. **Distilling** (recipes with `distillruns` above 0): the brew goes into a brewing stand and is run through it `distillruns` times (`distilltime` seconds per run, 40 if unset).
3. **Aging** (recipes with `age` above 0): put the brew into a barrel of the right wood (`wood`) for `age` "years". A year is `agingYearDuration` minutes (20 by default, one Minecraft day).
4. **Sealing** (optional): a Smoker (`sealingTableBlock`) crafted from 2 bottles over 4 planks seals a brew and hides its alcohol content.
5. **Drinking**: adds alcohol, runs effects and commands, and applies the drunk effects (stumbling, chat distortion, puking, login trouble at high levels).

Quality (1 to 5 stars, shown as bad / normal / good name variants) depends on how close cooking time, distill runs, wood and age were to the recipe, and on the recipe's `difficulty`.

## DwellerMC specifics
- **No surface needed.** Nothing in BreweryX uses sky light or rain. Cauldron water must come from buckets (or a cave water source); there is no rain to fill cauldrons.
- **Heat is easy**: lava lakes and springs generate in every cave biome except the Deep Dark ([layout](https://craftyplayz.github.io/DwellerMC-wiki/world/layout/)), and magma blocks work too.
- **Ingredient supply decides what is actually brewable.** Wheat, potatoes, sugar cane, cactus, cocoa and grass need farming under lights. Apples, sweet berries, eggs, milk and cookies are harder to get with no surface. Players are likely to start with the "any wood" recipes. Check which recipes your players can really make before announcing them.
- **Barrel wood.** Barrels use planks and stairs. Oak logs are available from azalea trees in Lush Caves; other woods have to come from loot, trading or structures. The recipes with `wood: 0` (any) are beer, red wine and apple cider.
- **Wakeup points and login rules.** `enableWake` moves a player who logs out drunk to a random "wakeup point" when they come back, and `enableLoginDisallow` makes very drunk players retry logging in. DwellerCore chooses its own spawn and respawn spots in the cave; set wakeup points (`/brew wakeup add`) only in safe, lit rooms, or turn `enableWake` off.
- **`homeType`.** The default `cmd: home` makes the player run `/home` (Essentials) when waking. Players with no home are handled by Essentials' `spawn-if-no-home`. `cmd: spawn` (EssentialsSpawn) is a safer choice here.
- **Protection plugins.** WorldGuard is installed and `useWorldGuard: true`, so barrel creation and opening respect WorldGuard flags. LWC, GriefPrevention, Towny, Lands, BlockLocker are not installed (their `use*` toggles are harmless). `useLogBlock` does nothing without LogBlock. CoreProtect does not log barrel contents.
- **Chat.** `enableChatDistortion` rewrites drunk players' chat. It affects EssentialsChat and what DiscordSRV relays. `distortCommands` already contains `/msg`, `/m`, `/w`, `/r`, `/tell` and others. Give `brewery.bypass.chatdistort` to staff if they should never be garbled.
- **Soul sand puke.** `pukeItem` drops SOUL_SAND for `pukeDespawntime` seconds; the items cannot be picked up. Cave floors fill briefly, no effect on gameplay.
- **Drunk and DwellerCore mobs.** Alcohol effects do not interact with mob levels, elites or depth.

## Commands
Main command `/breweryx`, aliases `/brewery` and `/brew` (from `commandAliases`; a restart is needed to change them).

| Subcommand | Permission | Use |
|---|---|---|
| `help` | none | List commands. |
| `info` | `brewery.cmd.info` | Your own drunkenness. |
| `info <player>` | `brewery.cmd.infoOther` | Another player's drunkenness. |
| `unlabel` | `brewery.cmd.unlabel` | Remove parts of a potion's label (hold the brew). |
| `version` | `brewery.cmd.version` | Plugin info. |
| `seal` | `brewery.cmd.seal` | Open the sealing interface. |
| `puke [player]` | `brewery.cmd.puke` / `pukeOther` | Make someone puke. |
| `wakeup add/remove/list/check/...` | `brewery.cmd.wakeup` | Manage wakeup points. |
| `create`, `give` | `brewery.cmd.create` | Give a finished brew. |
| `drink` | `brewery.cmd.drink` / `drinkOther` | Simulate drinking the held brew. |
| `copy`, `delete`, `static`, `set` | `brewery.cmd.copy` / `delete` / `static` / `set` | Edit held brews (`set` changes drunkenness and quality). |
| `reload` | `brewery.cmd.reload` | Reload `config.yml`, `recipes.yml`, `cauldron.yml`, `custom-items.yml`, language. |
| `itemName` | `brewery.cmd.itemname` | Show the material name of the held item (for recipes). |
| `reloadaddons` | `brewery.cmd.reloadaddons` | Reload addons. |
| `data` | `brewery.cmd.datamanager` | Data storage tools (migrate between storage types). |
| `distill`, `age`, `simulate`, `debuginfo`, `showstats` | admin | Test and debug helpers. |
| `player` | `brewery.cmd.player` | Set a player's values. |

Use `/brew help` in game for exact arguments on this build.

## Permissions
`brewery.user` is **true by default**, and contains: `brewery.createbarrel` (small, big), `brewery.openbarrel` (small, big, `mc` for vanilla barrels), `brewery.cauldron.time/insert/fill`, `brewery.cmd.unlabel/version/info`.

| Node | Default | Effect |
|---|---|---|
| `brewery.user` | true | Everything players need. |
| `brewery.mod` | false | `user` plus `cmd.wakeup` and `bypass.logindeny`. |
| `brewery.admin` | op | All commands and bypasses, `update` notices. |
| `brewery.*` | false | Everything including bypasses. |
| `brewery.bypass.logindeny` | false (in `mod`) | Always log in, even when very drunk. |
| `brewery.bypass.overdrink` | false | Not kicked when overdrinking (matters only if `enableKickOnOverdrink`). |
| `brewery.bypass.teleport` | false | Not teleported on login when drunk. |
| `brewery.bypass.chatdistort` | false | Chat is not distorted. |
| `brewery.sensitive.<percent>` | false | Alcohol sensitivity, for example `brewery.sensitive.150`. |
| `brewery.recovery.<n>` | false | Drunkenness lost per minute, for example `brewery.recovery.4`. |
| `brewery.update` | in admin | Update notices. |

### Recommended LuckPerms setup
Players need nothing; defaults cover brewing. For staff:

```
/lp creategroup mod
/lp group mod permission set brewery.mod true
/lp group mod permission set brewery.bypass.chatdistort true
/lp creategroup admin
/lp group admin permission set brewery.admin true
```

## Config (`plugins/BreweryX/config.yml`)
Changes need `/brew reload` unless noted. BreweryX rewrites its YAML files on save, so **comments you add are lost**.

### General
| Key | Default | Effect |
|---|---|---|
| `language` | `en` | `en, de, es, fr, it, ru, zh, uk, pl`. |
| `resolveUpdatesFrom` | `GITHUB` | Update source: GitHub, Snapshots, Spigot, None. |
| `autosave` | `10` | Minutes between saves. |
| `pluginPrefix` | `&2[BreweryX]&f ` | Message prefix. |
| `debug` | `false` | Debug logging. |
| `storage.type` | `SQLITE` | `FLATFILE`, `MYSQL`, `SQLITE`, `MONGODB`. |
| `storage.database` | `brewery-data` | Database or file name (`brewery-data.db`). |
| `storage.tablePrefix` | `brewery_` | Table prefix. |
| `storage.address/username/password` | `localhost / root / password` | Only for MySQL or MongoDB. |
| `commandAliases` | `brewery, brew` | Aliases of `/breweryx`. Restart needed. |
| `loadDataAsync` | `true` | No longer does anything. |

### Drunkenness
| Key | Default | Effect |
|---|---|---|
| `enableHome` | `true` | Use `homeType` when waking after heavy drinking. |
| `homeType` | `cmd: home` | `bed`, `cmd: home`, `cmd: spawn`, or any `cmd: <command>` run by the player. |
| `enableWake` | `true` | Wake at the nearest of two random wakeup points after logging out drunk. |
| `enableLoginDisallow` | `true` | Very drunk players may need several login attempts. |
| `enableKickOnOverdrink` | `false` | Kick for a moment at maximum alcohol. |
| `enablePuke` | `true` | Puke at high drunkenness. |
| `pukeItem` | `SOUL_SAND` | Item(s) dropped (list). |
| `pukeDespawntime` | `60` | Seconds until the puke items vanish. |
| `stumblePercent` | `100` | Stumble strength (0 and above 100 allowed). |
| `showStatusOnDrink` | `true` | Show drunkenness when drinking. |
| `drainItems` | `BREAD/4`, `MILK_BUCKET/2` | Items that sober you by that amount. |
| `hangoverDays` | `7` | Days drunkenness data stays after logout. |

### Brew items
| Key | Default | Effect |
|---|---|---|
| `alwaysShowQuality` | `true` | Star rating on the item. |
| `alwaysShowAlc` | `false` | Alcohol content on the item (hidden on sealed brews). |
| `alwaysShowAlcIndicator` | `true` | "Alcoholic" lore line. |
| `showBrewer` | `false` | Show who brewed it. |
| `showRuinedBrewHints` | `false` | Hints why a brew was ruined. |
| `enableEncode` | `false` | Scramble brew data. Brews then only work on servers with the same `encodeKey`. |
| `encodeKey` | random | Key used when encoding. |
| `colorInBarrels`, `colorInBrewer` | `true`, `true` | Color lore by quality in barrels / brewing stand. |
| `brewHopperDump` | `true` | Empty a brew into a hopper to keep the bottle. |
| `useOffhandForCauldron` | `false` | Offhand items also go into the cauldron. |
| `enableCauldronParticles` | `true` | Particles over cooking cauldrons. |
| `minimalParticles` | `false` | Fewer particles. |

### Sealing and barrels
| Key | Default | Effect |
|---|---|---|
| `craftSealingTable`, `enableSealingTable` | `true`, `true` | Craft / use the sealing table. |
| `sealingTableBlock` | `SMOKER` | Must be a container block. |
| `requireKeywordOnSigns` | `true` | Barrel sign must contain "barrel" (translated). |
| `agingYearDuration` | `20` | Minutes per aging year. |
| `ageInMCBarrels` | `true` | Age in vanilla barrels. |
| `maxBrewsInMCBarrels` | `6` | Brews allowed per vanilla barrel. |
| `exemptVanillaBarrels` | `true` | Vanilla barrels do not follow `onlyAllowBrewsInBarrels`. |
| `onlyAllowBrewsInBarrels` | `false` | Only Brewery drinks may go into barrels. |
| `newBarrelTypeAlgorithm` | `false` | Groups similar woods (oak and pale oak) when judging wood quality. |
| `openLargeBarrelEverywhere` | `true` | Open a large barrel by clicking any of its blocks. |
| `barrelInvSizeLarge`, `barrelInvSizeSmall` | `3`, `1` | Rows (1 to 6). Restart needed. |

### Protection plugins
`useWorldGuard`, `useLWC`, `useGriefPrevention`, `useTowny`, `useLands`, `useBlockLocker`, `useGMInventories` (all `true`), `useVirtualChestPerms` (`false`; for Residence-like plugins, may confuse anti-cheat), `useLogBlock` (`true`).

### Chat distortion
| Key | Default | Effect |
|---|---|---|
| `enableChatDistortion` | `true` | Garble drunk chat. |
| `logRealChat` | `false` | Log the original text to the console. |
| `distortCommands` | `/gl /global /fl /s /letter /g /l /lokal /local /mail send /m /msg /w /whisper /reply /r /t /tell` | Commands whose text is garbled. |
| `distortSignText` | `false` | Garble sign text. |
| `distortBypass` | `*,*` and `[,]` | Text between these pairs is not garbled. |
| `words` | long list | Replacement rules: `replace`, `to`, `pre`, `match`, `alcohol` (minimum drunkenness), `percentage`. Special `replace` values: `-space`, `-random`, `-all`, `-start`, `-end`. |

## Other files
- `recipes.yml`: every recipe. Keys per recipe: `enabled`, `name` (bad/normal/good separated by `/`), `ingredients` (`MATERIAL/amount`, `plugin:id/amount`, custom item id, or `brewery:recipeid/amount`), `cookingtime` (minutes), `distillruns`, `distilltime` (seconds, 0 = 40), `wood` (0 any, 1 Birch, 2 Oak, 3 Jungle, 4 Spruce, 5 Acacia, 6 Dark Oak, 7 Crimson, 8 Warped, 9 Mangrove, 10 Cherry, 11 Bamboo, 12 Cut Copper, 13 Pale Oak, or names), `age` (years), `color`, `difficulty` (1 to 10), `alcohol` (0 to 100, negative lowers), `lore` (prefix `+`, `++`, `+++` per quality), `servercommands`, `playercommands` (`/ 3s` delays), `drinkmessage`, `drinktitle`, `glint`, `customModelData` (`a/b/c` per quality), `effects` (`EFFECT/level/seconds`, ranges like `2-3`). The player-facing defaults are listed on the [player page](https://craftyplayz.github.io/DwellerMC-wiki/plugins/breweryx/). The disabled `ex` recipe documents every option.
- `cauldron.yml`: names, colors and particle colors of base potions per ingredient.
- `custom-items.yml`: custom item ids for recipes (`rasp`, `modelitem`, `blue-flowers` = cornflower or blue orchid, two examples).
- `brewery-data.db`: all barrels, cauldrons, brews in progress and player drunkenness. Back up with the server stopped.
- `languages/`: message files. Edit `languages/en.yml` for texts.
- `addons/`: addon jars.

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| "No permission to create barrel" | WorldGuard region denies it | Use the region's `build`/`chest-access` flags or the owner/member list. |
| Recipe never completes | Ingredients not in your world | Change the recipe or supply the item. |
| Players wake in odd places | `enableWake` with random wakeup points | Disable or place points safely. |
| Brews from another server are ruined | `enableEncode` with a different `encodeKey` | Keep the same key. |
| Cannot reload recipes | Syntax error in `recipes.yml` | Check the console after `/brew reload`. |

Back to the [admin index](../index.md) or [troubleshooting](../troubleshooting.md).

## Links
- [BreweryX docs](https://breweryx.breweryteam.dev/docs/welcome)
- [Default recipes](https://breweryx.breweryteam.dev/docs/for-server-owners/default-recipes)
- [BreweryX on GitHub](https://github.com/BreweryTeam/BreweryX)

# EssentialsX commands and permissions

Back to [EssentialsX](essentials.md). Config keys are on [Config reference](essentials-config.md).

Every command needs `essentials.<command>` unless the table says otherwise. Without a permission node, Essentials permissions are op-only. The last column is the recommendation for the LuckPerms `default` group (see [LuckPerms](essentials.md#luckperms)).

Commands marked "No" are staff tools or are held back on purpose. `/top`, `/tpr` and `/settpr` are disabled in the config. `/jump`, `/bottom` and the teleport commands are covered in [Ways to end up on the roof](essentials.md#ways-to-end-up-on-the-roof).

Aliases starting with `e` (for example `/ehome`) exist for every command and are left out.

## EssentialsXSpawn and EssentialsXChat commands

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/spawn [player]` | espawn | `essentials.spawn`, `essentials.spawn.others` | Teleports to the `/setspawn` spawn of your group, else `default`, else the world spawn | Yes (`.others` no) |
| `/setspawn [group]` | esetspawn | `essentials.setspawn` | Saves your position as the spawn for a group. Default group is `default`. `/setspawn newbies` sets the first-join spawn | No |
| `/toggleshout [player] [on\|off]` | etoggleshout | `essentials.toggleshout`, `.others` | Toggles shout mode. Only useful with `chat.radius` above 0 | No |

`essentials.spawn-on-join.exempt` (default false) skips the join teleport when `spawn-on-join` is on.

## Core commands

### Chat

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/broadcast` | bc, bcast, ebroadcast, shout | `essentials.broadcast` | Broadcasts a message to the entire server. | No |
| `/broadcastworld` | bcw, bcastw, ebroadcastworld, shoutworld | `essentials.broadcastworld` | Broadcasts a message to a world. | No |
| `/customtext` | - | `essentials.customtext` | Allows you to create custom text commands. | No |
| `/help` | ehelp | `essentials.help` | Views a list of available commands. | Yes |
| `/helpop` | ac, amsg, ehelpop | `essentials.helpop` | Message online admins. | Yes |
| `/ignore` | eignore, unignore, delignore, remignore, rmignore | `essentials.ignore` | Ignore or unignore other players. | Yes |
| `/info` | about, ifo, einfo, inform, news | `essentials.info` | Shows information set by the server owner. | Yes |
| `/list` | elist, online, playerlist, plist, who | `essentials.list` | List all online players. | Yes |
| `/mail` | email, memo | `essentials.mail` | Manages inter-player, intra-server mail. | Yes |
| `/me` | action, describe, eme | `essentials.me` | Describes an action in the context of the player. | Yes |
| `/motd` | emotd | `essentials.motd` | Views the Message Of The Day. | Yes |
| `/msg` | w, m, t, pm, emsg, tell | `essentials.msg` | Sends a private message to the specified player. | Yes |
| `/msgtoggle` | emsgtoggle | `essentials.msgtoggle` | Blocks receiving all private messages. | Yes |
| `/mute` | emute, silence, unmute | `essentials.mute` | Mutes or unmutes a player. | No |
| `/nick` | enick, nickname | `essentials.nick` | Change your nickname or that of another player. | Optional |
| `/r` | er, reply | `essentials.r` | Quickly reply to the last player to message you. | Yes |
| `/realname` | erealname | `essentials.realname` | Displays the username of a user based on nick. | Yes |
| `/rtoggle` | ertoggle, replytoggle | `essentials.rtoggle` | Change whether the recipient of the reply is last recipient or last sender | Optional |
| `/rules` | erules | `essentials.rules` | Views the server rules. | Yes |
| `/socialspy` | esocialspy | `essentials.socialspy` | Toggles if you can see msg/mail commands in chat. | No |

### Teleport and spawn

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/back` | eback, return | `essentials.back` | Teleports you to your location prior to tp/spawn/warp. | Yes |
| `/bottom` | ebottom | `essentials.bottom` | Teleport to the lowest block at your current position. | No |
| `/compass` | ecompass, direction | `essentials.compass` | Describes your current bearing. | Yes |
| `/depth` | edepth, height | `essentials.depth` | States current depth, relative to sea level. | No (misleading) |
| `/getpos` | coords, egetpos, position, whereami, getlocation, getloc | `essentials.getpos` | Get your current coordinates or those of a player. | Yes |
| `/jump` | j, ejump, jumpto | `essentials.jump` | Jumps to the nearest block in the line of sight. | No |
| `/near` | enear, nearby | `essentials.near` | Lists the players near by or around a player. | Yes |
| `/settpr` | esettpr, settprandom | `essentials.settpr` | Set the random teleport location and parameters. | No |
| `/top` | etop | `essentials.top` | Teleport to the highest block at your current position. | No |
| `/tp` | tele, teleport, etp, tp2p | `essentials.tp` | Teleport to a player. | No |
| `/tpa` | call, etpa, tpask | `essentials.tpa` | Request to teleport to the specified player. | Yes |
| `/tpaall` | etpaall | `essentials.tpaall` | Requests all players online to teleport to you. | No |
| `/tpacancel` | etpacancel | `essentials.tpacancel` | Cancel all outstanding teleport requests. Specify [player] to cancel requests with them. | Yes |
| `/tpaccept` | etpaccept, tpyes | `essentials.tpaccept` | Accepts teleport requests. | Yes |
| `/tpahere` | etpahere | `essentials.tpahere` | Request that the specified player teleport to you. | Yes |
| `/tpall` | etpall | `essentials.tpall` | Teleport all online players to another player. | No |
| `/tpauto` | etpauto | `essentials.tpauto` | Automatically accept teleportation requests. | No |
| `/tpdeny` | etpdeny, tpno | `essentials.tpdeny` | Rejects teleport requests. | Yes |
| `/tphere` | s, etphere | `essentials.tphere` | Teleport a player to you. | No |
| `/tpo` | etpo | `essentials.tpo` | Teleport override for tptoggle. | No |
| `/tpoffline` | otp, offlinetp, tpoff, tpoffline | `essentials.tpoffline` | Teleport to a player's last known logout location | No |
| `/tpohere` | etpohere | `essentials.tpohere` | Teleport here override for tptoggle. | No |
| `/tppos` | etppos | `essentials.tppos` | Teleport to coordinates. | No |
| `/tpr` | etpr, tprandom | `essentials.tpr` | Teleport randomly. | No |
| `/tptoggle` | etptoggle | `essentials.tptoggle` | Blocks all forms of teleportation. | Yes |
| `/world` | eworld | `essentials.world` | Switch between worlds. | No |

### Homes, warps, jails

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/delhome` | edelhome, remhome, rmhome | `essentials.delhome` | Removes a home. | Yes |
| `/deljail` | edeljail, remjail, rmjail | `essentials.deljail` | Removes a jail. | No |
| `/delwarp` | edelwarp, remwarp, rmwarp | `essentials.delwarp` | Deletes the specified warp. | No |
| `/home` | ehome, homes | `essentials.home` | Teleport to your home. | Yes |
| `/jailedplayers` | ejailedplayers, ejailed, ejp | `essentials.jailedplayers` | List all jailed players. | No |
| `/jails` | ejails | `essentials.jails` | List all jails. | No |
| `/renamehome` | erenamehome | `essentials.renamehome` | Renames a home. | Yes |
| `/sethome` | esethome, createhome | `essentials.sethome` | Set your home to your current location. | Yes |
| `/setjail` | esetjail, createjail | `essentials.setjail` | Creates a jail where you specified named [jailname]. | No |
| `/setwarp` | createwarp, esetwarp | `essentials.setwarp` | Creates a new warp. | No |
| `/togglejail` | jail, tjail, etogglejail, unjail | `essentials.togglejail` | Jails/Unjails a player, TPs them to the jail specified. | No |
| `/warp` | ewarp, warps | `essentials.warp` | List all warps or warp to the specified location. | Optional |
| `/warpinfo` | ewarpinfo | `essentials.warpinfo` | Finds location information for a specified warp. | No |

### Economy

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/balance` | bal, ebalance, money | `essentials.balance` | States the current balance of a player. | Yes |
| `/balancetop` | ebalancetop, baltop | `essentials.balancetop` | Gets the top balance values. | Yes |
| `/eco` | eeco, economy | `essentials.eco` | Manages the server economy. | No |
| `/pay` | epay | `essentials.pay` | Pays another player from your balance. | Yes |
| `/payconfirmtoggle` | epayconfirmtoggle, payconfirmoff, payconfirmon, payconfirm | `essentials.payconfirmtoggle` | Toggles whether you are prompted to confirm payments. | No |
| `/paytoggle` | epaytoggle, payoff, payon | `essentials.paytoggle` | Toggles whether you are accepting payments. | Optional |
| `/sell` | esell | `essentials.sell` | Sells the item currently in your hand. | No |
| `/setworth` | esetworth | `essentials.setworth` | Set the sell value of an item. | No |
| `/worth` | price, eworth | `essentials.worth` | Calculates the worth of items in hand or as specified. | No |

### Kits and items

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/book` | ebook | `essentials.book` | Allows reopening and editing of sealed books. | No |
| `/clearinventory` | ci, clean, clear, clearinvent, eclearinventory | `essentials.clearinventory` | Clear all items in your inventory. | No |
| `/clearinventoryconfirmtoggle` | eclearinventoryconfirmtoggle, clearinventoryconfirmoff, clearconfirmoff, clearconfirmon, clearconfirm | `essentials.clearinventoryconfirmtoggle` | Toggles whether you are prompted to confirm inventory clears. | No |
| `/condense` | econdense, compact, blocks, toblocks | `essentials.condense` | Condenses items into a more compact blocks. | No |
| `/createkit` | kitcreate, createk, kc, ck | `essentials.createkit` | Create a kit in game! | No |
| `/delkit` | edelkit, remkit, rmkit, deletekit | `essentials.delkit` | Deletes the specified kit. | No |
| `/disposal` | edisposal, trash | `essentials.disposal` | Opens a portable disposal menu. | No |
| `/enchant` | eenchant, enchantment | `essentials.enchant` | Enchants the item the user is holding. | No |
| `/firework` | efirework | `essentials.firework` | Allows you to modify a stack of fireworks. | No |
| `/give` | egive | `essentials.give` | Give a player an item. | No |
| `/hat` | ehat, head | `essentials.hat` | Get some cool new headgear. | No |
| `/item` | i, eitem | `essentials.item` | Spawn an item. | No |
| `/itemdb` | dura, durability, eitemdb, itemno | `essentials.itemdb` | Searches for an item. | Optional |
| `/itemlore` | lore, ilore, eitemlore | `essentials.itemlore` | Edit the lore of an item. | No |
| `/itemname` | iname, eitemname, itemrename, irename | `essentials.itemname` | Names an item. | No |
| `/kit` | ekit, kits | `essentials.kit` | Obtains the specified kit or views all available kits. | No |
| `/kitreset` | ekitreset, kitr, resetkit | `essentials.kitreset` | Resets the cooldown on the specified kit. | No |
| `/more` | emore | `essentials.more` | Fills the item stack in hand to specified amount, or to maximum size if none is specified. | No |
| `/potion` | epotion, elixer | `essentials.potion` | Adds custom potion effects to a potion. | No |
| `/powertool` | epowertool, pt | `essentials.powertool` | Assigns a command to the item in hand. | No |
| `/powertoollist` | epowertoollist, ptlist | `essentials.powertoollist` | Lists all current powertools. | No |
| `/powertooltoggle` | epowertooltoggle, ptt, pttoggle | `essentials.powertooltoggle` | Enables or disables all current powertools. | No |
| `/recipe` | formula, method, erecipe, recipes | `essentials.recipe` | Displays how to craft items. | Optional |
| `/repair` | fix, erepair | `essentials.repair` | Repairs the durability of one or all items. | No |
| `/showkit` | kitpreview, preview, kitshow | `essentials.showkit` | Show contents of a kit. | No |
| `/skull` | eskull, playerskull, head | `essentials.skull` | Set the owner of a player skull | No |
| `/unlimited` | eunlimited, ul, unl | `essentials.unlimited` | Allows the unlimited placing of items. | No |

### Crafting stations

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/anvil` | eanvil | `essentials.anvil` | Opens up an Anvil. | No |
| `/cartographytable` | ecartographytable, carttable | `essentials.cartographytable` | Opens up a cartography table. | No |
| `/enderchest` | echest, eenderchest, endersee, ec | `essentials.enderchest` | Lets you see inside an enderchest. | No |
| `/grindstone` | egrindstone | `essentials.grindstone` | Opens up a grindstone. | No |
| `/invsee` | einvsee | `essentials.invsee` | See the inventory of other players. | No |
| `/loom` | eloom | `essentials.loom` | Opens up a loom. | No |
| `/smithingtable` | esmithingtable, smithtable | `essentials.smithingtable` | Opens up a smithing table. | No |
| `/stonecutter` | estonecutter | `essentials.stonecutter` | Opens up a stonecutter. | No |
| `/workbench` | craft, wb, wbench, eworkbench | `essentials.workbench` | Opens up a workbench. | No |

### Player state and fun

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/afk` | eafk, away | `essentials.afk` | Marks you as away-from-keyboard. | Yes |
| `/antioch` | eantioch, grenade, tnt | `essentials.antioch` | A little surprise for operators. | No |
| `/beezooka` | ebeezooka, beecannon | `essentials.beezooka` | Throw an exploding bee at your opponent. | No |
| `/burn` | eburn | `essentials.burn` | Set a player on fire. | No |
| `/exp` | eexp, xp | `essentials.exp` | Give, set, reset, or look at a players experience. | No |
| `/ext` | eext, extinguish | `essentials.ext` | Extinguish players. | No |
| `/feed` | eat, efeed | `essentials.feed` | Satisfy the hunger. | No |
| `/fireball` | efireball, fireentity, fireskull | `essentials.fireball` | Throw a fireball or other assorted projectiles. | No |
| `/fly` | efly | `essentials.fly` | Take off, and soar! | No |
| `/gamemode` | adventure, adventuremode, creative, creativemode, egamemode, gm | `essentials.gamemode` | Change player gamemode. | No |
| `/god` | egod, godmode, tgm | `essentials.god` | Enables your godly powers. | No |
| `/heal` | eheal | `essentials.heal` | Heals you or the given player. | No |
| `/ice` | eice, efreeze | `essentials.ice` | Cools a player off. | No |
| `/kill` | ekill | `essentials.kill` | Kills specified player. | No |
| `/kittycannon` | ekittycannon | `essentials.kittycannon` | Throw an exploding kitten at your opponent. | No |
| `/lightning` | elightning, shock, smite, strike, thor | `essentials.lightning` | The power of Thor. Strike at cursor or player. | No |
| `/nuke` | enuke | `essentials.nuke` | May death rain upon them. | No |
| `/ping` | echo, eping, pong | `essentials.ping` | Pong! | Yes |
| `/playtime` | eplaytime | `essentials.playtime` | Shows a player's time played in game | Yes |
| `/ptime` | playertime, eptime | `essentials.ptime` | Adjust player's client time. Add @ prefix to fix. | No |
| `/pweather` | playerweather, epweather | `essentials.pweather` | Adjust a player's weather | No |
| `/rest` | erest | `essentials.rest` | Rests you or the given player. | No |
| `/seen` | eseen, alts | `essentials.seen` | Shows the last logout time of a player. | Yes |
| `/speed` | flyspeed, fspeed, espeed, walkspeed, wspeed | `essentials.speed` | Change your speed limits. | No |
| `/sudo` | esudo | `essentials.sudo` | Make another user perform a command. | No |
| `/suicide` | esuicide | `essentials.suicide` | Causes you to perish. | Yes |
| `/thunder` | ethunder | `essentials.thunder` | Enable/disable thunder. | No |
| `/time` | day, night, etime | `essentials.time` | Display/Change the world time. Defaults to current world. | No |
| `/vanish` | v, evanish | `essentials.vanish` | Hide yourself from other players. | No |
| `/weather` | rain, sky, storm, sun, eweather | `essentials.weather` | Sets the weather. | No |
| `/whois` | ewhois | `essentials.whois` | Determine basic information about the specified player. | No |

### Moderation

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/ban` | eban | `essentials.ban` | Bans a player. | No |
| `/banip` | ebanip | `essentials.banip` | Bans an IP address. | No |
| `/kick` | ekick | `essentials.kick` | Kicks a specified player with a reason. | No |
| `/kickall` | ekickall | `essentials.kickall` | Kicks all players off the server except the issuer. | No |
| `/tempban` | etempban | `essentials.tempban` | Temporary ban a user. | No |
| `/tempbanip` | etempbanip | `essentials.tempbanip` | Temporarily ban an IP Address. | No |
| `/unban` | pardon, eunban | `essentials.unban` | Unbans the specified player. | No |
| `/unbanip` | eunbanip, pardonip | `essentials.unbanip` | Unbans the specified IP address. | No |

### World and entities

| Command | Aliases | Permission | What it does | Default group |
|---|---|---|---|---|
| `/backup` | ebackup | `essentials.backup` | Runs the backup if configured. | No |
| `/bigtree` | ebigtree, largetree | `essentials.bigtree` | Spawn a big tree where you are looking. | No |
| `/break` | ebreak | `essentials.break` | Breaks the block you are looking at. | No |
| `/editsign` | sign, eeditsign | `essentials.editsign` | Edits a sign in the world. | No |
| `/essentials` | eessentials, ess, essversion | `essentials.essentials` | Reloads essentials. | No |
| `/gc` | lag, egc, mem, memory, uptime, tps | `essentials.gc` | Reports memory, uptime and tick info. | No |
| `/remove` | eremove, butcher, killall, mobkill | `essentials.remove` | Removes entities in your world. | No |
| `/spawner` | changems, espawner, mobspawner | `essentials.spawner` | Change the mob type of a spawner. | No |
| `/spawnmob` | mob, spawnentity, espawnmob | `essentials.spawnmob` | Spawns a mob. | No |
| `/tree` | etree | `essentials.tree` | Spawn a tree where you are looking. | No |

## Other permissions

Nodes that are not plain command permissions. "op" means only operators have it by default. Sign permissions (`essentials.signs.*`) are not listed: signs are disabled because `enabledSigns` is empty.

| Permission | Default | Effect |
|---|---|---|
| `essentials.*` | op | Give players with op everything by default |
| `essentials.afk.message` | op | Allows the player to set a custom AFK message |
| `essentials.afk.others` | op | Allows the player to set another player's AFK status |
| `essentials.afk.auto` | false | Players with this permission will be set to afk after a period of inaction as defined in the config file |
| `essentials.afk.kickexempt` | op | Players with this permission will not be kicked for being AFK |
| `essentials.back.others` | op | Allows access to the /back command for other players |
| `essentials.back.ondeath` | false | Players with this permission will have back location stored during death |
| `essentials.back.onteleport` | true | Players with this permission will have back location stored during any teleportation |
| `essentials.back.into.<world>` | op | Allows the player to use the /back command to travel to a specific world |
| `essentials.balance.others` | op | Allows view other players balance with the /balance command |
| `essentials.balancetop.force` | op | Allows access to force refresh the balancetop list |
| `essentials.balancetop.exclude` | false | Players with this permission are excluded from the balancetop |
| `essentials.ban.notify` | op | Allows the bearer to be notified when a player is banned |
| `essentials.ban.exempt` | false | Prevent a specified group or player from being banned |
| `essentials.ban.offline` | op | Allows access to the /ban command for offline players, which may in turn by used to ban exempt players who are offline |
| `essentials.banip.notify` | op | Allows the bearer to be notified when a player is banned |
| `essentials.book.author` | op | Allows access to edit book authors with the /book command |
| `essentials.book.others` | op | Allows access to edit other players books with the /book command |
| `essentials.book.title` | op | Allows access to edit book titles with the /book command |
| `essentials.break.bedrock` | op | Allows access to the /break command for bedrock |
| `essentials.chat.ignoreexempt` | false | Someone with this permission will not be ignored, even if they are on another persons ignore list |
| `essentials.chat.spy` | op | Allows the bearers to see all local chat messages, regardless of their proximity to the sender |
| `essentials.chat.spy.exempt` | op | Allows the bearer to be exempt from the local chat spy permission |
| `essentials.clearinventory.all` | op | Allows access to clear the inventory of all players with the /clearinventory command |
| `essentials.clearinventory.others` | op | Allows access to clear the inventory of other players with the /clearinventory command |
| `essentials.commandcooldowns.bypass` | op | Allows the bypassing of all command cooldowns |
| `essentials.commandcooldowns.bypass.<commandname>` | op | Allows the bypassing of the cooldown for a specific command |
| `essentials.kitreset.others` | op | Allows access to reset other players kits with the /kitreset command |
| `essentials.eco.loan` | op | Allows the bearer to possess a negative balance |
| `essentials.enchantments.allowunsafe` | op | Allows the bearer to enchant items with unsafe enchantments |
| `essentials.enchantments.<enchantment>` | op | Allows the bearer to use a specific enchantment with the /enchant command |
| `essentials.enderchest.modify` | op | Allows the bearer to modify other players enderchests |
| `essentials.enderchest.others` | op | Allows access to the /enderchest command for other players |
| `essentials.updatecheck` | op | The bearer will be notified of updates to EssentialsX |
| `essentials.exp.give` | op | Allows the bearer to give experience to themselves |
| `essentials.exp.give.others` | op | Allows the bearer to give experience to other players |
| `essentials.exp.others` | op | Allows the bearer to view other players experience |
| `essentials.exp.set` | op | Allows the bearer to set experience for themselves |
| `essentials.exp.set.others` | op | Allows the bearer to set experience for other players |
| `essentials.ext.others` | op | Allows access to the /ext command for other players |
| `essentials.feed.others` | op | Allows access to the /feed command for other players |
| `essentials.fireball.fireball` | op | Allows access to use the fireball in the /fireball command |
| `essentials.fireball.small` | op | Allows access to use the small fireball in the /fireball command |
| `essentials.fireball.large` | op | Allows access to use the large fireball in the /fireball command |
| `essentials.fireball.arrow` | op | Allows access to use the arrows in the /fireball command |
| `essentials.fireball.dragon` | op | Allows access to use the dragon in the /fireball command |
| `essentials.fireball.splashpotion` | op | Allows access to use the splash potion in the /fireball command |
| `essentials.fireball.lingeringpotion` | op | Allows access to use the lingering potion in the /fireball command |
| `essentials.fireball.trident` | op | Allows access to use the trident in the /fireball command |
| `essentials.fireball.skull` | op | Allows access to use the skull in the /fireball command |
| `essentials.fireball.egg` | op | Allows access to use the egg in the /fireball command |
| `essentials.fireball.snowball` | op | Allows access to use the snowball in the /fireball command |
| `essentials.firework.fire` | op | Allows the bearer to use the firework command to launch a copy of the firework in hand |
| `essentials.firework.multiple` | op | Allows the bearer to use the firework command to launch multiple fireworks |
| `essentials.fly.safelogin` | op | Bearers of this permission will be put into fly mode if they log in while in the air |
| `essentials.fly.others` | op | Allows access to the /fly command for other players |
| `essentials.gamemode.all` | op | Allows access to all gamemodes with the /gamemode command |
| `essentials.gamemode.others` | op | Allows access to the /gamemode command for other players |
| `essentials.gamemode.survival` | op | Allows access to the survival gamemode with the /gamemode command |
| `essentials.gamemode.creative` | op | Allows access to the creative gamemode with the /gamemode command |
| `essentials.gamemode.adventure` | op | Allows access to the adventure gamemode with the /gamemode command |
| `essentials.gamemode.spectator` | op | Allows access to the spectator gamemode with the /gamemode command |
| `essentials.gamemode.*` | op | Allows access to all gamemodes with the /gamemode command |
| `essentials.getpos.others` | op | Allows access to the /getpos command for other players |
| `essentials.give.item-all` | op | Allows access to the /give command for all items when permission-based-item-spawn is enabled |
| `essentials.give.item-<item-name>` | op | Allows access to the /give command for a specific item when permission-based-item-spawn is enabled |
| `essentials.god.others` | op | Allows access to the /god command for other players |
| `essentials.god.pvp` | op | Allows the bearer to attack players while in god mode |
| `essentials.hat.prevent-type.<item-name>` | op | Prevents the player from using the /hat command with the specified item |
| `essentials.hat.ignore-binding` | op | Allows the bearer to use the /hat command when they have equipped an item with curse of binding |
| `essentials.heal.others` | op | Allows access to the /heal command for other players |
| `essentials.help.<plugin-name>` | op | Allows access to the /help command for a specific plugin |
| `essentials.helpop.receive` | op | Allows the bearer to receive helpop messages |
| `essentials.kill.exempt` | op | Prevents the player from being killed by /kill |
| `essentials.kill.force` | op | Allows the bearer to be killed by /kill even if their death is canceled |
| `essentials.home.compass` | false | Point the player's compass at their first home. compass-towards-home-perm needs to be enabled in the configuration. |
| `essentials.home.others` | op | Allows access to teleport to other players homes with the /home command |
| `essentials.home.bed` | op | Allows access to the /home command for beds |
| `essentials.ice.others` | op | Allows access to the /ice command for other players |
| `essentials.invsee.equip` | op | Allows access to view items in armor slots with the /invsee command |
| `essentials.invsee.modify` | op | Allows access to modify items in other players inventories with the /invsee command |
| `essentials.invsee.preventmodify` | false | Prevents other players from modifying the players inventory. |
| `essentials.itemspawn.exempt` | op | Allows the bearer to spawn items in the item blacklist |
| `essentials.itemspawn.meta-chapter-<chapter>` | op | Allows the bearer to spawn specific books only, from book.txt. with the /give command. |
| `essentials.itemspawn.meta-author` | op | Allows the bearer to spawn items with author metadata |
| `essentials.itemspawn.meta-book` | op | Allows the bearer to spawn items with pre-filled content from book.txt |
| `essentials.itemspawn.meta-firework` | op | Allows the bearer to spawn items with firework metadata |
| `essentials.itemspawn.meta-head` | op | Allows the bearer to spawn items with head metadata |
| `essentials.itemspawn.meta-lore` | op | Allows the bearer to spawn items with lore metadata |
| `essentials.itemspawn.meta-title` | op | Allows the bearer to spawn items with title metadata |
| `essentials.itemname.format` | op | Allows access to the /itemname command with formatting text |
| `essentials.itemname.magic` | op | Allows access to the /itemname command with magic text |
| `essentials.itemname.color` | op | Allows access to the /itemname command with color text |
| `essentials.itemname.rgb` | op | Allows access to the /itemname command with RGB text |
| `essentials.itemname.prevent-type.<item-name>` | op | Prevents the player from using the /itemname command with the specified item |
| `essentials.jail.exempt` | op | Prevent a specified group or player from being jailed |
| `essentials.jail.allow.<command>` | op | Allows the bearer to use the specified command while jailed |
| `essentials.jail.notify` | op | Allows the bearer to be notified when other players are jailed |
| `essentials.jail.allow-break` | op | Allows the bearer to break blocks while jailed |
| `essentials.jail.allow-place` | op | Allows the bearer to place blocks while jailed |
| `essentials.jail.allow-block-damage` | op | Allows the bearer to damage blocks while jailed |
| `essentials.jail.allow-interact` | op | Allows the bearer to interact with blocks while jailed |
| `essentials.jump.lock` | op | Allows access to the /jump lock command |
| `essentials.keepxp` | false | Allows the user to keep their exp on death, instead of dropping it. |
| `essentials.joinfullserver` | op | Allows the to join the server even if it is full |
| `essentials.whitelist.bypass` | op | Allows a player to join the server even if they are not whitelisted. |
| `essentials.kick.exempt` | false | Prevents the player from being kicked. |
| `essentials.kick.notify` | op | Allows the bearer to be notified when other players are kicked |
| `essentials.kickall.exempt` | op | Prevents the player from being kicked by /kickall |
| `essentials.kit.exemptdelay` | false | Exempts you from the kit delay feature, this affects signs as well as command. |
| `essentials.kit.others` | op | Allows access to the /kit command for other players |
| `essentials.kits.*` | op | Allows access to all kits |
| `essentials.kits.<kit-name>` | op | Allows access to a specific kit |
| `essentials.lightning.others` | op | Allows access to the /lightning command for other players |
| `essentials.list.hidden` | op | Allow access to view hidden players in the /list command |
| `essentials.mail.send` | op | Allows access to send mail with the /mail command |
| `essentials.mail.sendall` | op | Allows access to send mail to all players with the /mail command |
| `essentials.mail.sendtemp` | op | Allows access to send temporary mail with the /mail command |
| `essentials.mail.clear.others` | op | Allows access to clear other players mail with the /mail command |
| `essentials.mail.clearall` | op | Allows access to clear all mail with the /mail command |
| `essentials.msg.format` | op | Allows access to the /msg command with formatting text |
| `essentials.msg.magic` | op | Allows access to the /msg command with magic text |
| `essentials.msg.color` | op | Allows access to the /msg command with color text |
| `essentials.msg.rgb` | op | Allows access to the /msg command with RGB text |
| `essentials.msg.url` | op | Allows access to send URLs in the /msg command |
| `essentials.msg.multiple` | op | Allows access to send messages to multiple players with the /msg command |
| `essentials.msgtoggle.others` | op | Allows access to the /msgtoggle command for other players |
| `essentials.msgtoggle.bypass` | op | Allows the bearer to bypass the msgtoggle setting for other players |
| `essentials.mute.notify` | op | Allows the bearer to be notified when other players are muted |
| `essentials.mute.exempt` | false | Prevent a specified group or player from being muted |
| `essentials.mute.unlimited` | op | Allows the bearer to override the max-mute-time setting |
| `essentials.mute.offline` | op | Allows access to mute offline players |
| `essentials.near.maxexempt` | op | Allows the bearer to bypass the radius limit for the /near command |
| `essentials.near.others` | op | Allows access to the /near command for other players |
| `essentials.near.exclude` | false | If the player should be excluded from near lookups. |
| `essentials.nick.color` | op | Allows access to the /nick command with color text |
| `essentials.nick.format` | op | Allows access to the /nick command with formatting text |
| `essentials.nick.magic` | op | Allows access to the /nick command with magic text |
| `essentials.nick.rgb` | op | Allows access to the /nick command with RGB text |
| `essentials.nick.others` | op | Allows access to the /nick command for other players |
| `essentials.nick.blacklist.bypass` | op | Allows the bearer to bypass the nickname blacklist |
| `essentials.nick.changecolors` | op | Allows the bearer to change **only** the color of their nickname |
| `essentials.nick.allowunsafe` | false | If a player has this, they can set their username to any value. Use with caution, as this has the potential to break userdata files. |
| `essentials.nick.hideprefix` | false | Players with this permission will not have the nickname prefix applied to them |
| `essentials.nocommandcost.all` | op | Allows the bearer to use all commands without cost |
| `essentials.nocommandcost.<command>` | op | Allows the bearer to use the specified command without cost |
| `essentials.oversizedstacks` | op | Allows the bearer to spawn oversized stacks |
| `essentials.pay.multiple` | op | Allows access to pay multiple players with the /pay command |
| `essentials.pay.offline` | op | Allows access to pay offline players with the /pay command |
| `essentials.potion.<potion-type>` | op | Allows access to the /potion command for a specific potion type |
| `essentials.potion.apply` | op | Allows access to the /potion apply command |
| `essentials.powertool.append` | op | Allows access to add multiple commands to a powertool |
| `essentials.ptime.others` | op | Allows access to the /ptime command for other players |
| `essentials.pvpdelay.exempt` | op | Allows the bearer to bypass the pvp delay setting |
| `essentials.pweather.others` | op | Allows access to the /pweather command for other players |
| `essentials.repair.all` | op | Allows access to the /repair all command |
| `essentials.repair.armor` | op | Allows access to the /repair armor command |
| `essentials.repair.enchanted` | op | Allows access to use the /repair command on enchanted items |
| `essentials.rest.others` | op | Allows access to the /rest command for other players |
| `essentials.seen.banreason` | op | Allows access to the /seen command with ban reason |
| `essentials.seen.ip` | op | Allows access to the /seen command with IP address |
| `essentials.seen.location` | op | Allows access to the /seen command with location |
| `essentials.seen.uuid` | op | Allows access to the /seen command with UUID |
| `essentials.seen.firstlogin` | op | Allows access to the /seen command with first login time |
| `essentials.seen.whitelist` | op | Allows access to the /seen command with whitelist status |
| `essentials.seen.ipsearch` | op | Allows access to use IP addresses with the /seen command |
| `essentials.seen.alts` | op | Allows access to the /seen command with alts |
| `essentials.seen.extra` | op | Allows access to the /seen command with extra information |
| `essentials.sell.bulk` | op | Allows access to bulk sell items with the /sell command |
| `essentials.sell.hand` | op | Allows access to sell the item in hand with the /sell command |
| `essentials.renamehome.others` | op | Allows access to rename other players homes with the /renamehome command |
| `essentials.sethome.bed` | false | Allows the player to right click a bed during daytime to update their 'bed' home. |
| `essentials.sethome.multiple` | op | Raises the limit of homes to a specific number defined in the config |
| `essentials.sethome.multiple.unlimited` | op | Allows access to set unlimited homes with the /sethome command |
| `essentials.sethome.others` | op | Allows access to set other players homes with the /sethome command |
| `essentials.delhome.others` | op | Allows access to delete other players homes with the /delhome command |
| `essentials.silentjoin` | false | Allow to join silently |
| `essentials.silentjoin.vanish` | false | Allow to join silently, and get put in vanish mode |
| `essentials.silentquit` | false | Suppress leave/quit messages for users with this permission. |
| `essentials.skull.modify` | op | Allows access to modify other players skulls with the /skull command |
| `essentials.skull.others` | op | Allows access to creating other players skulls with the /skull command |
| `essentials.skull.spawn` | op | Allows access to spawn a skull with the /skull command |
| `essentials.sleepingignored` | op | Allows the bearer to not be required to sleep to skip the night |
| `essentials.socialspy.others` | op | Allows access to the /socialspy command for other players |
| `essentials.spawner.delay` | op | Allows access to set the delay of a spawner with the /spawner command |
| `essentials.spawner.*` | op | Allows access to set the type of a spawner with the /spawner command to all types |
| `essentials.spawner.<mob-type>` | op | Allows access to set the type of a spawner with the /spawner command to a specific type |
| `essentials.spawnerconvert.*` | op | Allows the bearer to place spawners of any type |
| `essentials.spawnerconvert.<mob-type>` | op | Allows the bearer to place spawners of a specific type |
| `essentials.spawnmob.stack` | op | Allows access to spawn mobs in stacks with the /spawnmob command |
| `essentials.spawnmob.*` | op | Allows access to spawn all mobs with the /spawnmob command |
| `essentials.spawnmob.<mob-type>` | op | Allows access to spawn a specific mob with the /spawnmob command |
| `essentials.speed.others` | op | Allows access to the /speed command for other players |
| `essentials.speed.fly` | op | Allows access to the /speed command for fly speed |
| `essentials.speed.walk` | op | Allows access to the /speed command for walk speed |
| `essentials.speed.bypass` | op | Allows the bearer to bypass the speed limit set in the config |
| `essentials.sudo.exempt` | false | Prevents the player from being sudo'ed by another user |
| `essentials.sudo.multiple` | op | Allows access to the /sudo command for multiple players |
| `essentials.teleport.timer.bypass` | op | Allows the bearer to bypass the teleport delay |
| `essentials.teleport.timer.move` | op | Allows the bearer to move while waiting for a teleport |
| `essentials.tempban.exempt` | false | Prevent a specified group or player from being tempbanned |
| `essentials.tempban.offline` | op | Allows access to tempban offline players |
| `essentials.tempban.unlimited` | op | Allows the bearer to override the max-tempban-time setting |
| `essentials.time.set` | op | Allows access to set the time with the /time command |
| `essentials.time.world.all` | op | Allows access to set the time for all worlds with the /time command |
| `essentials.togglejail.offline` | op | Allows access to the /togglejail command for offline players |
| `essentials.tpauto.others` | op | Allows access to the /tpauto command for other players |
| `essentials.tp.others` | op | Allows access to teleporting to other users with the /tp command |
| `essentials.tp.position` | op | Allows access to teleporting to a position with the /tp command |
| `essentials.tptoggle.others` | op | Allows access to the /tptoggle command for other players |
| `essentials.unlimited.item-all` | op | Allows access to the /unlimited command for all items when permission-based-item-spawn is enabled |
| `essentials.unlimited.item-<item-name>` | op | Allows access to the /unlimited command for a specific item when permission-based-item-spawn is enabled |
| `essentials.unlimited.item-bucket` | op | Allows access to the /unlimited command for buckets of water and lava |
| `essentials.unlimited.others` | op | Allows access to the /unlimited command for other players |
| `essentials.vanish.others` | op | Allows access to the /vanish command for other players |
| `essentials.vanish.pvp` | op | Allows the bearer to attack players while in vanish mode |
| `essentials.vanish.see` | op | Allows the bearer to see other players in vanish mode |
| `essentials.vanish.effect` | op | Applies invisibility effects to the player when they are in vanish mode |
| `essentials.vanish.interact` | op | Allows the bearer to interact with players in vanish mode |
| `essentials.vanish.pickup` | false | Allows the bearer to pick up items while in vanish mode |
| `essentials.version` | op | Allows access to the /version command |
| `essentials.warp.overwrite.*` | op | Allows the bearer to overwrite all warps |
| `essentials.warp.overwrite.<warp-name>` | op | Allows the bearer to overwrite a specific warp |
| `essentials.warp.list` | op | Allows access to the /warp list command |
| `essentials.warp.others` | op | Allows access to the /warp command for other players |
| `essentials.whois.ip` | op | Allows access to the /whois command with IP address |
| `essentials.worlds.<world-name>` | op | Allows access to the specific world with various commands |
| `essentials.editsign.unlimited` | op | Allows the bearer to exceed the 15 character limit on signs |
| `essentials.editsign.color` | op | Allows the bearer to use color codes on signs |
| `essentials.editsign.format` | op | Allows the bearer to use formatting codes on signs |
| `essentials.editsign.magic` | op | Allows the bearer to use magic codes on signs |
| `essentials.editsign.rgb` | op | Allows the bearer to use RGB codes on signs |
| `essentials.editsign.waxed.exempt` | op | Allows the bearer to edit waxed signs |
| `essentials.keepinv` | false | Controls whether players keep their inventory on death. |
| `essentials.playtime.others` | op | Allows access to the /playtime command for other players |
| `essentials.teleport.cooldown.bypass.tpa` | true | If the player does not have this permission, /tpa will have cooldown even with the parent bypass perm |
| `essentials.teleport.cooldown.bypass.back` | true | If the player does not have this permission, /back will have cooldown even with the parent bypass perm |
| `essentials.exempt` | false | Parent permission to be exempt from many moderator actions |

## Links

- [Command reference](https://essentialsx.net/wiki/Command-Reference.html)
- [Permissions](https://essentialsx.net/wiki/Permissions.html)

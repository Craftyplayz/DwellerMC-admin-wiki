# Disguises (LibsDisguises, packetevents, BlockDisguise)

Three jars work together:

| Plugin | Version | Job |
|---|---|---|
| **packetevents** | 2.14.0 | Packet library. No commands, no config of its own. Required by LibsDisguises. |
| **LibsDisguises** | 26.10.24 (free "Github" build 1836) | Makes players or mobs look like another entity (packet-level, nothing changes server-side). |
| **BlockDisguise** | 1.0.0 (our own plugin) | Wear a renamed item on your head to disguise as a block or mob. Depends on both plugins above. |

Player-facing summary: [Disguises](https://craftyplayz.github.io/DwellerMC-wiki/plugins/disguises/).

## LibsDisguises

### Free version limits

The startup log says: `You are running the free version, commands limited to non-players and operators (Console, Command Blocks, Admins)`. So `/disguise` and friends only work for ops and the console. Players cannot disguise themselves with commands, whatever permissions you give. The premium jar (Spigot resource) removes that and enables saving disguises, translations and more (`premium.yml`). BlockDisguise uses the plugin's API, so it is not affected by the command limit.

### Commands (free build: ops and console only)

| Command | Aliases | Effect |
|---|---|---|
| `/disguise <type> [options]` | `/d`, `/dis`, `/disg` | Disguise yourself. Examples: `/disguise zombie`, `/disguise player Notch`. |
| `/disguiseplayer <player> <type>` | `/dplayer` | Disguise another player. |
| `/disguiseentity <type>` | `/dentity` | Right-click an entity to disguise it. |
| `/disguiseradius <radius> <type>` | `/dradius` | Disguise entities nearby (max radius 50). |
| `/disguiseselector`, `/undisguise*` | | Selector / remove variants (`/undisguise`, `/undisguiseplayer`, `/undisguiseentity`, `/undisguiseradius`, `/undisguiseselector`). |
| `/disguisemodify`, `...player`, `...radius`, `...entity`, `...selector` | `/dmodify` | Change options of an existing disguise. |
| `/disguiseclone` | | Copy a disguise from an entity. |
| `/disguiseviewself` | `/dvs` | Toggle seeing your own disguise. |
| `/disguiseviewbar` | `/dvb` | Toggle the notify message. |
| `/disguiseanimation` and `player/radius/selector/entity` variants | | Play an animation. |
| `/copydisguise`, `/grabskin`, `/grabhead`, `/savedisguise` | | Utilities (saving needs premium). |
| `/disguisehelp` | `/dhelp` | Help. |
| `/libsdisguises` | `/ld` | Plugin admin (reload, count, update, config, mods). |

### Permissions (plugin.yml)

| Permission | Default | Effect |
|---|---|---|
| `libsdisguises.seecmd` (parent of all `seecmd.*`) | true | Show commands in tab completion only. |
| `libsdisguises.seecmd.<command>` | via parent | Same, per command. |
| `libsdisguises.selfdisguises` | true | May see their own disguise. |
| `libsdisguises.seethrough` | false | See through disguises (staff). |
| `libsdisguises.hidename` | false | Ignore the forced name above a disguise. |
| `libsdisguises.pvp`, `libsdisguises.pve` | false | Ignore the PvP/PvE restrictions from `events.yml`. |
| `libsdisguises.noactionbar` | false | Hide the notify action bar for this player. |
| `libsdisguises.disguiseanimation` and the four other animation nodes | op | Animations. |
| `libsdisguises.reload`, `update`, `count`, `mods`, `config`, `debug`, `json`, `metainfo`, `permtest`, `scoreboardtest`, `multiname`, `grabhead` | no explicit default (plugin.yml leaves them to ops) | Admin tools. |
| `libsdisguises.ratelimitbypass` | none | Bypass the 500 ms command rate limit. |

Permissions to actually use disguises are of the form `libsdisguises.disguise.<type>` (and `.<option>`); because the free build blocks players anyway, no default-group grants are needed. Leave the `default` group without any `libsdisguises.*`.

### Config files (`plugins/LibsDisguises/configs/`)

#### libsdisguises.yml

| Key | Default | Effect |
|---|---|---|
| `MineSkinAPIKey` | `N/A` | For `/grabskin` uploads. |
| `ContactMojangServers` | true | Skin/UUID lookups. |
| `UUIDResolvingUrl` | Mojang profile URL | Name to UUID. |
| `NotifyUpdate`, `AutoUpdate`, `UpdatesBranch` | true, true, `SAME_BUILDS` | Updates. **AutoUpdate downloads new jars on its own**; consider false so the jar version stays pinned. |
| `NeverUpdatePacketEvents` | false | Stops LD from replacing the packetevents jar. Set true if you want to control packetevents yourself. |
| `DynamicFilenames`, `VerboseConfig`, `ChangedConfig`, `UpdateConfig` | true, false, false, true | Config handling. |
| `RandomUUIDs`, `UniquePlayerUUID` | true, true | UUIDs of disguise entities. |
| `SaveGameProfiles`, `UpdateGameProfiles`, `DynamicExpiry` | true, true, false | Skin cache. |
| `VerboseLogging` | false | Debug. |
| `Compatibility.VoiceChat` | true | Hook for voice chat plugins. |

#### displays.yml

| Key | Default | Effect |
|---|---|---|
| `ShowNamesAboveDisguises` | false | Show the real name above a disguise. |
| `NameAboveHeadAlwaysVisible` | true | Name stays visible through blocks. |
| `NameAboveDisguise` | `%complex%` | Name format. |
| `OverrideCustomNames` | true | LD's name handling overrides a disguised entity's custom name. |
| `PlayerNames` | `ARMORSTANDS` | How player names are drawn above disguises. |
| `Scoreboard.Collisions` | true | Disguised players collide. |
| `Scoreboard.DisableFriendlyInvisibles` | false | Team option. |
| `Scoreboard.WarnConflict` | true | Warn when another plugin fights over teams. |
| `Scoreboard.CopyPlayerTeamInfo` | true | Copy the player's team prefix/suffix. |
| `Scoreboard.UpdateHealth` | false | Health objective. |
| `Scoreboard.ModifyScoreboards` | true | LD edits scoreboards to hide names. |
| `HideDisguisedPlayersFromTab` | false | Remove from tab list. |
| `ShowPlayerDisguisesInTab` | false | Show player-type disguises in the list. |
| `NotifyBar` | `ACTION_BAR` | `NONE`, `ACTION_BAR` or `BOSS_BAR` message "You are disguised as...". |
| `BossBarColor`, `BossBarStyle` | `GREEN`, `SOLID` | If boss bar. |

#### events.yml

| Key | Default | Effect |
|---|---|---|
| `StopShulkerDisguisesFromMoving` | true | Shulker disguise fix. |
| `UndisguiseOnWorldChange` | false | Remove on world change. |
| `UndisguiseOnBlockPlace`, `UndisguiseOnBlockBreak` | false, false | Remove on building. |
| `MonstersIgnoreDisguises` | false | Hostile mobs ignore players disguised as mobs. |
| `DisablePvP`, `DisablePvE` | false, false | Disguised players cannot attack. |
| `BlowDisguisesWhenAttacking`, `BlowDisguisesWhenAttacked` | false, false | Remove on combat. |
| `RetaliationCombat`, `PvPTimer` | false, 5 | Combat window (seconds). |
| `HideDeathMessages` | false | Hide death messages of disguised players. |

#### self_disguise.yml, protocol.yml, dangerous.yml, commands.yml, premium.yml, sounds.yml

| File | Key | Default | Effect |
|---|---|---|---|
| self_disguise | `RemoveArmor`, `RemoveHeldItem`, `RemoveCreativeEquipment` | true, false, false | Hide your own gear from yourself while disguised. |
| self_disguise | `ViewSelfDisguises` / `ViewSelfDisguisesDefault` | true / true | You see your own disguise. |
| self_disguise | `SelfDisguisesScaling`, `SelfDisguisesScaleMin`, `SelfDisguisesScaleMax` | false, 0.06, 1 | Scale own view (BlockDisguise overrides min 0.0625 and max 16 at runtime). |
| self_disguise | `HearSelfDisguise`, `TallSelfDisguises` | true, `SCALED` | Disguise sounds, oversize handling. |
| protocol | `PlayerHideArmor`, `AddEntityAnimations`, `SendAttackAnimations`, `SendVelocity`, `MaxHealthDeterminedByEntity`, `MiscDisguisesForLiving`, `FancyHiddenTabs`, `RandomDisguiseOptions` | true | Packet behavior. |
| protocol | `LoadIntoRegistries` | false | Registry injection. |
| protocol | `PacketsEnabled.*` | all true | Which packet types LD handles. Do not disable. |
| protocol | `UUIDVersion` | 4 | UUID version. |
| protocol | `DyeableSheep`, `DyeableWolf`, `DyeableCat`, `CarpetableLlama`, `SaddleableHorse`, `AllayItemSwitchable` | false | Let players interact with disguised mobs. |
| dangerous | `ModifyBoundingBox` | false | Hitbox changes. |
| dangerous | `TablistRemoveDelay`, `PlayerDisguisesTablistExpiresMove` | 3, 100 | Tab list timing. |
| dangerous | `Ticker.Projectiles` | true | Projectile disguises tick. |
| dangerous | `DisabledDisguises` | `TEXT_DISPLAY, ITEM_DISPLAY, BLOCK_DISPLAY, INTERACTION` | Disallowed disguise types. |
| dangerous | `DisabledMethods` | `setYModifier, setNameYModifier, setNameViewRange, setInvisible, setUnsafeSize, setScalePlayerToDisguise, setServerBoundingBox` | Disallowed disguise options. |
| commands | `DisableInvisibility`, `SaveUserPreferences` | false, true | Command options. |
| commands | `Permissions.ExplicitDisguises`, `Permissions.SeeCommands` | false, `TRUE` | Permission model. |
| commands | `DisguiseRadiusMax`, `UndisguiseRadiusMax`, `DisguiseEntityExpire`, `DisguiseCloneExpire`, `DisguiseCloneSize` | 50, 50, 10, 10, 3 | Limits. |
| commands | `MinScale`, `MaxScale` | 0, 50 | Allowed scale values. |
| premium | `SaveDisguises.Players/Entities`, `KeepDisguises.PlayerDeath`, `Translations` | false | Premium only. |
| sounds | `DisguiseSounds`, `PlayIdleSounds`, `SoundCategory` | true, false, `DISGUISE` | Disguise sound behavior. |
| disguises | `Disguises.libraryaddict`, `Custom-Entities` | example | Saved custom disguises. |

## packetevents

Jar `packetevents-spigot-2.14.0.jar`. Nothing to configure. It loads before LibsDisguises. The log confirms `You are running the latest release: 2.14.0` and loaded block mappings for 26.3. Other packetevents plugins could ask for a different version; keep one jar. TAB has the option `compensate-for-packetevents-bug` (default false) if the tab list misbehaves with it. Do not let LibsDisguises auto-replace it unexpectedly (`NeverUpdatePacketEvents`).

## BlockDisguise (our plugin)

No commands, no permissions, no config file. It does one thing: while a player wears a **helmet** with a **custom (anvil) name**, they are disguised.

| Rename the helmet item to | Item | Result |
|---|---|---|
| `transform` | Any placeable block that is not a carved pumpkin, head or skull | The player looks like that block (a falling-block disguise). Blocks cannot be hit directly, so an invisible interaction hitbox of 1x1 is added. |
| `transform entity` | An item from the table below | The player looks like that mob or entity. |

The name check ignores case and surrounding spaces. Take the item off and the disguise is removed (checked every 5 ticks and on equipment change).

Extra behavior:
- Sneaking twice within 1 second while block-disguised centers you on your block.
- Death, quitting and spectator mode remove the disguise.
- The player's hitbox is scaled to the disguise (`setScalePlayerToDisguise`), with self-view limits 0.0625 to 16.
- Mob disguises hide the player's real held and worn items from observers; your own helmet slot stays visible so you can take it off.
- The notify bar is turned off for these disguises (no action bar text).
- If the server started on Peaceful, LibsDisguises has no size data for hostile mobs and BlockDisguise skips the disguise and logs `No size data for <ENTITY>. Was the server started on peaceful?` once.

### Item table

| Group | Item to entity |
|---|---|
| Farm and mounts | Porkchop pig, beef cow, chicken chicken, mutton sheep, rabbit rabbit, saddle horse, hay block donkey, golden carrot mule, bone block skeleton horse, green carpet zombie horse, white carpet llama, blue carpet trader llama, cactus camel, dead bush camel husk, goat horn goat, red mushroom mooshroom |
| Pets and wild | Cooked beef wolf, string cat, cocoa beans ocelot, feather parrot, sweet berries fox, bamboo panda, ice polar bear, honeycomb bee, gray dye bat, lily pad frog, armadillo scute armadillo, torchflower seeds sniffer, warped fungus strider, amethyst shard allay, dried ghast happy ghast |
| Water | Cod, salmon, tropical fish, pufferfish (same names), ink sac squid, glow ink sac glow squid, heart of the sea dolphin, turtle scute turtle, axolotl bucket axolotl, tadpole bucket tadpole, nautilus shell nautilus, dead brain coral zombie nautilus, prismarine shard guardian, sponge elder guardian, kelp drowned |
| Villagers, golems | Emerald villager, emerald block wandering trader, iron block iron golem, snow block snow golem, copper ingot copper golem |
| Undead | Rotten flesh zombie, golden apple zombie villager, sand husk, enchanted golden apple giant, bone skeleton, packed ice stray, brown mushroom bogged, charcoal parched, coal wither skeleton, phantom membrane phantom, gold nugget zombified piglin, crimson fungus zoglin |
| Monsters | Gunpowder creeper, spider eye spider, fermented spider eye cave spider, slime ball slime, stone silverfish, redstone witch, end stone enderman, chorus fruit endermite, shulker shell shulker, breeze rod breeze, resin clump creaking, yellow dye sulfur cube |
| Nether, raids | Blaze rod blaze, ghast tear ghast, magma cream magma cube, gold ingot piglin, gold block piglin brute, cooked porkchop hoglin, white banner pillager, iron ingot vindicator, totem of undying evoker, enchanted book illusioner, leather ravager, iron nugget vex |
| Bosses | Nether star wither, dragon egg ender dragon, echo shard warden |
| Boats, rafts | Every boat and chest boat type (acacia, birch, cherry, dark oak, jungle, mangrove, oak, pale oak, poplar, spruce) and bamboo raft / chest raft, as the matching item |
| Minecarts | Minecart, chest, furnace, hopper, TNT, command block minecart, spawner (spawner minecart) |
| Other | Armor stand, end crystal, item frame, glow item frame, painting, TNT, egg, snowball, ender pearl, arrow, spectral arrow, firework rocket, wind charge, splash potion, lingering potion, experience bottle, ender eye, fire charge (fireball), blaze powder (small fireball), dragon breath (dragon fireball), wither rose (wither skull), popped chorus fruit (shulker bullet), white dye (llama spit), lapis lazuli (experience orb), lead (leash knot) |

## Interactions with DwellerMC

- **Players disguised as hostile mobs are not Enemy entities.** The disguise is packets only; the player stays a Player, so DwellerCore does not level them, give them boss bars or name tags, and mobs do not drop levels or rewards for them. Nothing in DwellerCore reads the disguise.
- **Hostile mobs still attack disguised players** (`MonstersIgnoreDisguises: false`). Set it to true in `events.yml` if you want mob disguises to work as stealth. That also lets players dodge fights with BlockDisguise helmets, so decide on purpose.
- **Disguised real mobs keep their levels.** If staff disguise a leveled mob (`/disguiseentity`), the underlying entity keeps its level, health and attributes. The DwellerCore name tag is a custom name; LibsDisguises' `OverrideCustomNames: true` may replace what viewers see above the disguise. Check one in game.
- **Action bar clash:** DwellerCore's depth HUD is an action bar, and LibsDisguises' `NotifyBar: ACTION_BAR` also writes to the action bar when a player (staff) disguises with a command. They will flicker over each other. Set `NotifyBar: NONE` in `displays.yml`. BlockDisguise already disables its own notify bar.
- **Giant disguises in tunnels:** hitbox scaling means a player wearing a dragon egg (`transform entity`) is as large as an ender dragon, and a giant (enchanted golden apple) is huge. In 3-block-high caves they will get stuck and see through walls. Consider whether those items should be allowed. There is no config in BlockDisguise to remove entries; changing it needs a code change.
- **Boss disguises:** wither, dragon and warden disguises are visual only; no boss bars or abilities.
- **Anyone can use BlockDisguise.** There is no permission. A helmet and an anvil rename are enough, so every player can disguise as any listed mob or block. If you want it restricted, use a permission check in the plugin (code change) or remove the jar.
- **Players near leveled mobs:** mobs only see the real player. A player disguised as a creeper is still targeted by zombies.
- **TAB:** TAB has a soft dependency on LibsDisguises. Both edit scoreboard teams for name tags. LD's `Scoreboard.WarnConflict: true` logs if they fight. If name tags look wrong for disguised players, set LD `Scoreboard.ModifyScoreboards: false` or TAB `scoreboard-teams.enabled: false`.
- **Citizens:** NPCs can use `/npc disguise` (needs LibsDisguises, present).
- **Plan, DiscordSRV:** show the real player; disguises are invisible to them.
- **dangerous.yml:** `DisabledMethods` lists `setScalePlayerToDisguise` and the disabled disguise types include `INTERACTION`, both used by BlockDisguise's API calls. Test that block disguises still resize and can be hit after each LibsDisguises update.

## Recommended settings

- `displays.yml`: `NotifyBar: NONE`.
- `libsdisguises.yml`: `AutoUpdate: false`, `NeverUpdatePacketEvents: true` (update both by hand together).
- `events.yml`: leave `MonstersIgnoreDisguises: false` unless you want stealth disguises.
- Do not start the server on Peaceful (BlockDisguise cannot disguise as hostile mobs then).

## Problems

| Symptom | Cause / fix |
|---|---|
| `/disguise` says no permission for non-ops | Free build limit. Ops and console only. |
| Helmet does nothing | Name must be exactly `transform` or `transform entity` (case-insensitive), item must be in the table, and it must be in the helmet slot. |
| Log: `No size data for ...` | Server started on Peaceful. Restart on a higher difficulty. |
| Disguise disappears after teleport or world change | BlockDisguise reapplies within 5 ticks; if `UndisguiseOnWorldChange` is true it is removed first. |
| Player stuck in blocks | Large disguise scaling; remove the helmet. |
| Version errors after a Minecraft update | LibsDisguises and packetevents must both support 26.3; update both. |

See also [TAB](tab.md), [Citizens](citizens.md), [troubleshooting](../troubleshooting.md), [index](../index.md).

## Links

- [LibsDisguises on GitHub](https://github.com/libraryaddict/LibsDisguises)
- [LibsDisguises docs](https://docs.libsdisguises.net/)
- [packetevents docs](https://github.com/retrooper/packetevents)

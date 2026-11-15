# Veinminer (+ Enchantment addon)

Mine one ore and the whole connected vein breaks. Two jars: **Veinminer** (`veinminer-paper-2.12.3`, plugin name `Veinminer`) and the addon **Veinminer_Enchantment** (`veinminer-enchant-2.11.2`). Authors: Miraculixx, Max Bossing. Server-side only; players need no mod.

Player page: <https://craftyplayz.github.io/DwellerMC-wiki/plugins/veinminer/>. Related: [CraftyTools](craftytools.md), [PickupBot](pickupbot.md), [Troubleshooting](../troubleshooting.md).

## Enchant-only setup (the owner decision)

**No config change is needed.** Installing `veinminer-enchant` next to `veinminer-paper` is the switch. At startup Veinminer looks for a plugin named `veinminer_enchantment` (`Veinminer.class`, `PluginManager.getPlugin`). If it is found, Veinminer sets an internal flag `enchantmentActive`, and from then on every veinmine requires the item in the main hand to carry the enchantment `veinminer_enchantment:veinminer`. There is no key for this in `settings.json`. Remove the addon jar and everyone can veinmine again.

If the flag is on and the tool has no such enchantment, nothing is mined. It applies to **all groups**, not only Ores.

Load order is handled: the addon's `paper-plugin.yml` declares `Veinminer` as a required dependency loaded BEFORE it.

The test server already runs this way: both jars loaded and logged `Veinminer_Enchantment loaded (paper)`. Before relying on it, confirm in game that a plain pickaxe does not veinmine and an enchanted one does.

**Can it be pre-created before first start?** The recommended behaviour is already the default, so nothing needs pre-creating. For custom values, create `plugins/Veinminer/settings.json` and `plugins/Veinminer/enchantmentSettings.json` before first start; both are only generated when missing. Edit with the server stopped.

## Files (all in `plugins/Veinminer/`)

| File | Created by | What |
|---|---|---|
| `settings.json` | Veinminer | General settings (below). |
| `groups.json` | Veinminer | Block groups with tools and per-group overrides. Default: one group `Ores`. |
| `blocks.json` | Veinminer | Extra single blocks mineable with any tool. Default `[]`. |
| `enchantmentSettings.json` | Veinminer_Enchantment | Enchantment cost and which items can get it. Read at bootstrap, so a **restart** is needed. |
| `update/` | Veinminer | Downloaded newer jars (2.14.0 at time of writing). Paper does not load from here; see [Updating](#updating). |

`/veinminer reload` (permission `veinminer.reload`) reloads settings, groups and blocks. Not the enchantment file.

## Commands

| Command | Permission | What |
|---|---|---|
| `/veinminer help` | none seen | Help text with links. |
| `/veinminer toggle` | `veinminer.toggle` | Turn Veinminer on/off for the whole server. |
| `/veinminer settings <setting> [<value>]` | `veinminer.settings` | Show or change a setting. Omit value to read. |
| `/veinminer blocks <add/remove> <block>` | `veinminer.blocks` | Blocks that veinmine with every tool. |
| `/veinminer groups ...` | `veinminer.groups` | `create <name> [blocks]`, `edit <name> add-block / remove-block / add-tool / remove-tool`, overrides. |
| `/veinminer presets` | `veinminer.groups` | Adds ready-made groups (Logs, Leaves, Crops, Stones, NetherStones, Terracotta, ConcretePowder, Grassy). **Do not use** if only ores should veinmine. |
| `/veinminer reload` | `veinminer.reload` | Reload config files. |

`/veinminer enchant ...` (in the newer upstream README) is **not in 2.12.3**; edit `enchantmentSettings.json` instead.

## Permissions

Veinminer declares no permission defaults in its plugin file. The command check is: **ops pass, everyone else needs `hasPermission(node)`**. With LuckPerms nothing is granted unless you grant it.

| Node | Default | Effect |
|---|---|---|
| `veinminer.use` | none | Only checked when `permissionRestricted: true`. With the default `false` nobody needs it. |
| `veinminer.toggle`, `.settings`, `.blocks`, `.groups`, `.reload` | op only | Admin commands. Give to admins only. |

There is no permission for the enchantment: anyone holding an enchanted tool can use it.

## settings.json (generated defaults on this server)

| Key | Default | Effect |
|---|---|---|
| `cooldown` | `20` | Ticks before the same player can veinmine again. |
| `mustSneak` | `false` | Player must sneak. |
| `delay` | `0` | Ticks between blocks of the vein. `0` = all at once. (Upstream docs say 1; the generated file says 0.) |
| `maxChain` | `100` | Max blocks broken per veinmine. |
| `needCorrectTool` | `true` | Block must drop something with that tool. |
| `searchRadius` | `1` | How far from a block to look for the next vein block (max 5). |
| `permissionRestricted` | `false` | `true` = only players with `veinminer.use`. |
| `mergeItemDrops` | `false` | `true` = all drops appear at the first block. |
| `autoUpdate` | `false` here | Upstream default is `true`; the generated file here says `false`, yet `update/` holds 2.14.0 jars. |
| `decreaseDurability` | `true` | Each extra block costs tool durability. |
| `hungerPerBlock` | `0.0` | Exhaustion added per block (vanilla block break is 0.005). |
| `miningSpeedModifier` | `0.0` | Slows mining based on vein size. |
| `separateGroupMining` | `false` | `false`: all blocks of a group chain together (touching coal, iron and diamond mine as one). `true`: only the same block type. |
| `dontDisplayUpdatesAndIWillNotAskForSupportBeforeUpdatingISwear` | `false` | Hide update notices. |
| `debug` | `false` | Log decisions per break. |
| `client.allow` / `require` / `translucentBlockHighlight` / `allBlocks` | `true / false / true / false` | For the optional client hotkey mod. Players without it are unaffected. Keep `require: false`. |
| `client.overrides.*` | `null` | Per-setting override for hotkey-mod players. `null` = use the normal setting. |

Veinmining never happens in **Creative**. It stops when the tool has too little durability left (it will not break the tool). Drops use the tool, so **Fortune and Silk Touch apply**; XP is dropped.

## groups.json (default)

One group `Ores`: blocks `#c:ores`, tools `#minecraft:pickaxes`, all overrides `null`. `#c:ores` comes from Veinminer's bundled datapack (`Veinminer/provided-c-tags`): coal, iron, copper, gold, lapis, redstone, diamond, emerald (stone and deepslate), nether gold, nether quartz, ancient debris. Per-group `override` can change cooldown, mustSneak, delay, maxChain, needCorrectTool, searchRadius, permissionRestricted, decreaseDurability, hungerPerBlock, miningSpeedModifier, separateGroupMining. `minEnchantLevel` does **not exist** in this version.

## enchantmentSettings.json

| Key | Default | Effect |
|---|---|---|
| `minCost` | `15` | Lowest enchanting-table cost where it can appear. |
| `maxCost` | `65` | Highest cost where it can appear. |
| `anvilCost` | `7` | Anvil cost. |
| `pickaxeOnly` | `false` | `false`: pickaxes, axes, shovels, hoes (`#minecraft:enchantable/mining`). `true`: pickaxes only. |

Fixed in code: max level 1, weight 1 (rarest), main hand only.

## How players get the enchantment

The addon adds the enchantment to the vanilla tags `in_enchanting_table`, `tradeable` and `non_treasure`.

- **Enchanting table:** yes, when the offered cost is 15 or higher. Rare because weight is 1.
- **Villager trades:** in the `tradeable` tag, so librarians can sell the book.
- **Anvil:** book + tool, base cost 7.
- **Chest loot:** not added to `on_random_loot` in this version, so it should not appear in chests.
- **Ops:** `/enchant <player> veinminer_enchantment:veinminer`.

## Interactions

- **WorldGuard / CoreProtect:** each vein block fires `VeinminerEvent`, a subclass of `BlockBreakEvent`, before the block is removed; if cancelled the block is skipped. So regions and CoreProtect should see it.
- **[CraftyTools](craftytools.md) 3x3 pickaxe:** area blocks use `player.breakBlock`, which fires a normal `BlockBreakEvent` each. With the enchant on the 3x3 pickaxe, Veinminer can react to those: if the centre block is ore the vein mines and the 20-tick cooldown blocks further veins in that swing; if the centre is stone and an ore is inside the 3x3, that ore can start a veinmine. CraftyTools only acts on the block the player is looking at, so there is no loop.
- **[PickupBot](pickupbot.md):** PickupBot counts `VeinminerEvent` as a block break, so vein drops belong to the miner. But it only collects within 5 blocks. Large veins leave drops behind. Use `mergeItemDrops: true`.
- **DwellerCore ores:** most ore is in cave walls, in vanilla-sized veins. `maxChain: 100` exceeds any single vein. Touching veins of different ores chain together while `separateGroupMining` is false.
- **Bedrock:** not in any group, never mined.

## Recommended settings

- `enchantmentSettings.json`: `"pickaxeOnly": true`.
- `settings.json`: `"mergeItemDrops": true`; consider `"mustSneak": true` (single ores without the whole vein); optionally a small `hungerPerBlock` (tune; no verified value); keep `permissionRestricted: false`.
- Do not run `/veinminer presets`.

## Updating

Upstream latest is 2.14.0 (log: `veinminer is outdated (26.3). Installed: 2.12.3`). Jars sit in `plugins/Veinminer/update/`. To update, replace both jars in `plugins/` and restart. 2.14.0 adds `/veinminer enchant`, `minEnchantLevel` and more; read the changelog and back up `plugins/Veinminer/` first. Keep both jars on the same release.

## Problems

- **Everyone can still veinmine:** the addon is missing or failed to load. Check `/plugins` and the log line above.
- **Enchant never offered:** weight 1 is rare and cost range is 15-65. Test with `/enchant`.
- **`enchantmentSettings.json` change ignored:** restart needed.
- **Lag on huge veins:** many item drops; lower `maxChain` or set `delay` to 1 or more.

## Links

- <https://modrinth.com/plugin/veinminer>
- <https://modrinth.com/datapack/veinminer-enchantment>
- <https://github.com/MiraculixxT/Veinminer>

# CraftyTools

Owner-written plugin (`crafty-tools-1.0.0`, Paper 26.3, Java 25) that adds two expensive netherite tools: the **3x3 Pickaxe** and the **Paxel**. Source: `d:/HDD/backup/!Git/3x3pickaxe` (README.md, `src/`). Player page: <https://craftyplayz.github.io/DwellerMC-wiki/plugins/craftytools/>. Related: [Veinminer](veinminer.md), [PickupBot](pickupbot.md).

!!! warning "Recipes are intentionally disabled for now"
    Recipes are intentionally disabled for now (`recipes: false` in `plugins/CraftyTools/config.yml`). Enable by setting `true` and restarting.

## Config: `plugins/CraftyTools/config.yml`

Created on first start (exists on the test server).

| Key | Default | Effect |
|---|---|---|
| `recipes` | `false` | `true` registers both crafting recipes. `false`: tools only come from `/craftytool give`. Restart needed. Tools already given keep working either way. |

## Commands and permissions

| Command | Permission | Default | What |
|---|---|---|---|
| `/craftytool give <tool> [player]` | `craftytools.give` | op | Gives a fresh tool (`3x3_pickaxe` or `paxel`) to yourself or an online player. Full inventory: the rest drops at their feet. |
| `/craftylore` | none | everyone | Rewrites the lore on the Crafty tool in your main hand. |

LuckPerms: give `craftytools.give` to staff only.

## Tools

Tools are identified by a hidden data tag `craftytools:tool` (`3x3_pickaxe`, `paxel`), not by name. Renaming in an anvil keeps them working. Never rename the ids.

### 3x3 Pickaxe (`3x3_pickaxe`)

A netherite pickaxe. Breaking a block also breaks the other 8 blocks of the 3x3 square flat against the face you hit.

Recipe (only when `recipes: true`): `TNT, Diamond Block, TNT / Netherite Block, Netherite Pickaxe, Netherite Block / Nether Star, End Crystal, Nether Star`.

- Triggers on every break, sneaking or not.
- Skips air, fluids, unbreakable blocks (bedrock, barriers, portals) and containers (chests, barrels, shulker boxes, furnaces...).
- Each area block is broken with `player.breakBlock`: its own `BlockBreakEvent` fires, so protection plugins can stop it. Drops follow netherite pickaxe rules, Fortune/Silk Touch apply per block, ores give XP.
- Durability: 1 per block. Area mining stops with 2 left, so the tool ends at 1 and never breaks.
- Only runs if the broken block is the one your crosshair hits.

### Paxel (`paxel`)

A netherite pickaxe with a custom tool component: netherite speed (9) on pickaxe, axe and shovel blocks, correct drops. Mines one block at a time.

Recipe (only when `recipes: true`): `Netherite Pickaxe, Netherite Axe, Netherite Shovel / Diamond Block, Nether Star, Diamond Block / Netherite Block, End Crystal, Netherite Block`.

Right-click axe actions: strip logs, wood, stems, hyphae and bamboo blocks; scrape copper one stage; remove wax; copper doors and trapdoors just open. Right-click shovel actions: dirt paths, put out lit campfires. Each action costs 1 durability.

## Interactions

- **[Veinminer](veinminer.md):** both tools are netherite pickaxes, so they count as pickaxes for Veinminer and can carry the Veinmine enchantment. See that page for how the two combine.
- **[PickupBot](pickupbot.md):** area drops are collected: every area block fires a break event that PickupBot records. Default radius 5 covers a 3x3.
- **WorldGuard:** checked per block, so part of a 3x3 can break at a region edge.
- **CoreProtect:** logs each block as a normal player break.
- **Bedrock:** never broken by the 3x3, so the roof and floor are safe.
- **DwellerCore ores:** most ore is in cave walls (buried ore is kept only 10%), which a 3x3 hits well. A swing costs up to 9 durability.

## Problems

- No special behaviour: the item is a plain netherite pickaxe, not made by `/craftytool give`.
- Players ask how to craft: recipes are off by design.
- `recipes` changed with no effect: restart (no reload).

## Links

- Source and README: `d:/HDD/backup/!Git/3x3pickaxe`

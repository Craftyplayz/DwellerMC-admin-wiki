# DwellerTools config

File: `plugins/DwellerTools/config.yml` (default: `DwellerTools/src/main/resources/config.yml`). Keys sit under `grappling-hook:`, `drill:`, `hammer:` or `paxel:`, one section per tool.

**When changes apply:** a full **restart**. There is no reload command and the config is read once at startup, recipe and messages included.

**Bad values never stop the server.** A wrong type, a number outside the range, an unknown item or a broken recipe logs a warning that names the key, and the default is used. A key missing from your file uses the default from the jar.

## What it does

DwellerTools holds custom tools: the Grappling Hook, and the [Drill, Hammer and Paxel](#drill-hammer-and-paxel) mining tools. The first is the **Grappling Hook**: right-click throws a hook at a block and a climbable rope drops from it, sneak + right-click rappels down from an edge. Players climb the ropes like ladders: hold jump to go up, sneak to hang, let go to slide down. Ropes are temporary and removed by the plugin.

How a rope is built: one `minecraft:light` block (level 0, invisible and not solid) per rope block, made climbable by the plugin's own datapack (it adds `minecraft:light` to `#minecraft:climbable`), plus one `item_display` per block that shows the rope model, and one `item_display` for the hook at the top. Ropes only ever replace air.

## Install

1. Build: `./gradlew build` in `DwellerTools/`. The jar is `build/libs/DwellerTools-<version>.jar`.
2. Copy it into `plugins/`. Needs Paper 26.3 and Java 25. It is a Paper plugin that loads at `STARTUP` and registers its datapack in the bootstrapper, like DwellerCore.
3. Make sure [DwellerCore](config-core.md#resource-pack) sends the resource pack. Without the pack players see a plain carrot on a stick and string.
4. Restart. The `dwellertools` datapack should show in `/datapack list enabled`.

!!! warning "Restart, never /reload"
    The datapack is registered before worlds load. After a `/reload` or a jar added to a running server, mobs can't climb ropes until a full restart. Players still can: the plugin moves them itself.

## grappling-hook

| Key | Default | Unit | Effect |
|---|---|---|---|
| `enabled` | `true` | boolean | `false`: the item does nothing, the recipe is not registered and no rope is placed. Hooks players already own stay in their inventories. |
| `disabled-worlds` | `[]` | list of world names | Worlds where right-click only shows `world-disabled`. Names are case-sensitive. Empty = every world, including the surface world. |

### item

| Key | Default | Unit | Effect |
|---|---|---|---|
| `item.name` | `<white>Grappling Hook` | MiniMessage | Item name. Only applies to hooks made afterwards. |
| `item.lore` | two grey lines | list of MiniMessage | Lore lines. Only applies to hooks made afterwards. |
| `item.base-item` | `carrot_on_a_stick` | item id | Vanilla item the hook is built on. It should do nothing on right-click and must not be a crafting ingredient or fuel. Existing hooks keep their old base. An unknown id logs a warning and the default is used. |
| `item.model` | `dwellermc:grappling_hook` | model id | Item model from the [resource pack](resource-pack.md) (`assets/dwellermc/items/grappling_hook.json`). A model the pack does not have shows the base item. With the pack it currently looks like a tripwire hook (the item json points at `minecraft:item/tripwire_hook`). |
| `item.durability` | `64` | uses, 1 to 100000 | Uses before it breaks. Existing hooks keep their old maximum. |
| `item.durability-per-use` | `1` | durability, 0 to 100000 | Lost per successful grapple or rappel. `0` = never wears. Failed throws cost nothing. Unbreaking can save the use, as on vanilla tools. |
| `item.repair-material` | `iron_ingot` | item id | Anvil repair material, 25% per item, like iron tools. Existing hooks keep their old material. |
| `item.allowed-enchantments` | `[unbreaking, "dwellerenchants:unbreakable"]` | list of enchantment ids | The only enchantments an anvil may put on the hook (`minecraft:` is assumed when no namespace is given). Mending is left out on purpose. Ids of enchantments that do not exist (yet) are fine; they just never match. |

The anvil accepts: repairing with `repair-material`, combining two hooks, adding an enchanted book whose enchantments are all allowed, and renaming. Anything else (other items, books with a disallowed enchantment, a hook on the right-hand side only) gives no result. The crafting grid never accepts a hook as an ingredient, which also stops the vanilla grid repair that would wipe the item.

### recipe

| Key | Default | Unit | Effect |
|---|---|---|---|
| `recipe.enabled` | `true` | boolean | `false`: the hook can't be crafted. Other plugins can still give the item. |
| `recipe.shape` | `["  H", " DB", "SB "]` | 1 to 3 rows of 1 to 3 characters | Shaped crafting grid, a space is an empty slot. All rows need the same length. The mirrored shape also works. |
| `recipe.ingredients` | `H: tripwire_hook`, `D: diamond`, `B: iron_block`, `S: string` | character to item id | Every non-space character in `shape` needs an item. |
| `recipe.give-on-join` | `true` | boolean | Every player has the recipe in their recipe book from the moment they join. `false` = they have to unlock it some other way (for example another plugin granting it). |

A bad shape or ingredient list logs `Config grappling-hook.recipe: ... Using the default recipe`, and the whole default recipe is used, not a half-fixed one. A recipe that clashes with another plugin's recipe is not checked.

### cooldown

| Key | Default | Unit | Effect |
|---|---|---|---|
| `cooldown` | `8` | seconds, 0 to 3600 | One cooldown shared by grapple and rappel, started by a successful throw and shown on the item. `0` = none. Always the current config value. |

### grapple

| Key | Default | Unit | Effect |
|---|---|---|---|
| `grapple.range` | `24` | blocks, 1 to 128 | Maximum distance from the player's eyes to the hooked block. Also the `{range}` in `out-of-range`. |
| `grapple.rope-length` | `24` | blocks, 1 to 256 | Maximum rope length. The rope hangs down from its top until it meets anything that is not air, or reaches this many blocks, or the bottom of the world. |
| `grapple.ceiling` | `true` | boolean | The hook can catch the underside of a block. The rope starts in the block below it. |
| `grapple.wall` | `true` | boolean | The hook can catch the side of a block. The rope starts in the block in front of that face. |
| `grapple.ledge` | `true` | boolean | The hook can catch the top of a block. The rope starts beside the hooked block on the player's side, at the same height, so it hangs over the edge. |
| `grapple.excluded-blocks` | `[]` | list of block ids | Blocks the hook never catches, even though they are full solid blocks. Blocks that are not full and opaque (glass, leaves, slabs, fences) can never be hooked, whatever this says. Use it to keep a protected building unhookable. |
| `grapple.flight-ticks` | `8` | ticks (20 per second), 1 to 59 | How long the hook visibly flies before the rope drops. The rope appears one tick after the flight ends. |

The cooldown starts when the hook is thrown, and durability is used when the rope lands. If the spot gets filled during the flight (a block placed, another rope), no rope is placed, the player gets `no-room`, no durability is used and the cooldown is cleared.

### rappel

| Key | Default | Unit | Effect |
|---|---|---|---|
| `rappel.rope-length` | `48` | blocks, 1 to 256 | Maximum rope length, counted from the block in front of the player's feet. The player is teleported onto the rope's top block, facing back at the edge. |

A rappel needs solid ground under the player, an air block in front of their feet with air below it, and free space above that block. Water or lava below the rope is allowed.

### rope

| Key | Default | Unit | Effect |
|---|---|---|---|
| `rope.max-per-player` | `2` | ropes, 1 to 100 | Ropes one player can have at once. Placing one more removes their oldest. |
| `rope.despawn-seconds` | `15` | seconds, 1 to 3600 | Time a rope stays after the last player gets off it, or after it was placed if nobody gets on. A rope with a player on it never despawns. Reaching the top or the floor only starts the timer. |
| `rope.hold-at-end` | `true` | boolean | A rope that ends in mid-air or above lava holds the player at its last block instead of letting them slide off. Sneak and jump together to let go (for one second). `false` = they slide off the end. A rope that ends on solid ground never holds. |
| `rope.climb-speed` | `4` | blocks per second, 0.5 to 20 | Speed a player inside a rope goes up while holding jump without sneak, also from the ground. `4` is vanilla ladder speed. The plugin sets the player's speed every tick, so this works on every client. Flying players and players who just let go (sneak + jump) are left alone. |
| `rope.slide-speed` | `3` | blocks per second, 0.5 to 20 | Fastest a player slides down a rope while pressing neither jump nor sneak. Sneak hangs still (off the ground only). `3` is vanilla ladder speed. Fall distance is reset each tick the plugin moves the player. |
| `rope.model` | `dwellermc:rope` | model id | Item model of one rope block, from the [resource pack](resource-pack.md). Currently the vanilla chain block model. |
| `rope.hook-item` | `tripwire_hook` | item id | Vanilla item shown as the hook in flight and at the top of the rope. Not part of the resource pack. |
| `rope.check-ticks` | `5` | ticks, 1 to 20 | How often ropes are checked: who is on them, cut ropes, broken anchors, despawn, mining boost. Lower reacts faster and costs more; despawn and cut timing are rounded to this step. Climbing itself runs every tick, whatever this is set to. |

### mining

| Key | Default | Unit | Effect |
|---|---|---|---|
| `mining.enabled` | `true` | boolean | Players on a rope who are not on the ground mine as fast as on the ground. Vanilla mines 5 times slower while off the ground. |
| `mining.speed-multiplier` | `5` | factor, 1 to 10 | Multiplier on block break speed while on a rope. `5` cancels the vanilla penalty exactly; above 5 mining on a rope is faster than on the ground. Applied as a temporary `block_break_speed` modifier (`dwellertools:rope_mining`), removed when the player leaves the rope, quits or joins. |

### sounds

| Key | Default | Effect |
|---|---|---|
| `sounds.throw` | `minecraft:entity.fishing_bobber.throw` | Where the hook is thrown. |
| `sounds.attach` | `minecraft:block.chain.place` | When the rope drops or the rappel starts. |
| `sounds.fail` | `minecraft:block.dispenser.fail` | Only to the player, together with a failure message. |

`""` = no sound. Sound ids are not validated; an unknown one plays nothing.

### messages

Shown on the action bar (MiniMessage). `{range}` and `{length}` are replaced.

| Key | Default | When |
|---|---|---|
| `messages.out-of-range` | `Nothing to hook within {range} blocks` | The ray hit no block within `grapple.range`. |
| `messages.cant-hook` | `The hook can't catch that` | Not a full solid block, an excluded block, or that face is switched off (`ceiling`, `wall`, `ledge`). |
| `messages.no-room` | `No room for a rope there` | The rope's top block is not free, the spot was filled during the flight, or (rappel) the block above the rope's top is not free. |
| `messages.rope-exists` | `There's already a rope here` | A rope already occupies the top block. |
| `messages.no-drop` | `No drop here: stand at an edge and face it` | Rappel without an edge in front of you, or while not standing on solid ground. |
| `messages.world-disabled` | `The grappling hook doesn't work here` | The world is in `disabled-worlds`. |
| `messages.short-rope` | `The rope doesn't reach the bottom ({length} blocks)` | The rope ends in air or above lava. `{length}` is its length. |

## Drill, Hammer and Paxel

Three mining tools built on netherite tools, each with its own top-level section. All three have the same `enabled`, `item.*` and `recipe.*` keys, described once here. Item settings (name, lore, model, durability, repair material) are stored on the item when it is crafted, so **existing items keep their old values**. The three recipes are placeholders until final ones are picked.

### Shared keys (`drill.`, `hammer.`, `paxel.`)

| Key | Default | Unit | Effect |
|---|---|---|---|
| `enabled` | `true` | boolean | `false`: no recipe. Hammer: no square. Paxel: no dirt paths. Drill: anvils and grindstones still keep its Efficiency level, but it can no longer be repaired with its repair material in an anvil. Items players already own stay in their inventories and still work as plain netherite tools; a Paxel keeps its mining rules because they are stored on the item. |
| `item.name` | `<white>Drill`, `<white>Hammer`, `<white>Paxel` | MiniMessage | Item name. |
| `item.lore` | one grey line each | list of MiniMessage | Lore lines. |
| `item.model` | `dwellermc:drill`, `dwellermc:hammer`, `dwellermc:paxel` | model id | Item model from the [resource pack](resource-pack.md). A model the pack does not have shows the plain netherite tool. With the pack the Drill and Hammer use the vanilla netherite pickaxe model and the Paxel the netherite axe model. |
| `item.durability` | `2031` | uses, 1 to 100000 | Uses before it breaks. Vanilla netherite is 2031. Existing items keep their old maximum. |
| `item.repair-material` | `netherite_ingot` | item id | Anvil repair material, 25% per item. An unknown id logs `Config <tool>.item.repair-material: unknown item ...` and the default is used. |
| `recipe.enabled` | `true` | boolean | `false`: the tool can't be crafted. Other plugins can still give the item. |
| `recipe.shape` | `["RRR", " N ", " N "]`, `["DDD", " N ", " N "]`, `["EEE", " N ", " N "]` | 1 to 3 rows of 1 to 3 characters | Shaped grid, a space is an empty slot. The mirrored shape also works. |
| `recipe.ingredients` | Drill: `R: redstone_block`, `N: netherite_ingot`. Hammer: `D: diamond_block`, `N: netherite_ingot`. Paxel: `E: emerald_block`, `N: netherite_ingot` | character to item id | Every non-space character in `shape` needs an item. |
| `recipe.give-on-join` | `true` | boolean | Every player has the recipe in their recipe book from the moment they join. |

A bad shape or ingredient list logs `Config <tool>.recipe: ... Using the default recipe` and the whole default recipe is used.

Anvil rules, the same for all three: repair with `item.repair-material`, combine two tools of the same kind, add enchanted books (vanilla rules decide which enchantments fit the base item), rename. A tool never merges into a different item; any other combination gives no result. The crafting grid never accepts one of these tools as an ingredient, which also stops the vanilla grid repair that would wipe the item.

### drill

| Key | Default | Unit | Effect |
|---|---|---|---|
| `efficiency-level` | `10` | level, 1 to 255 | Efficiency level on the Drill. Vanilla tops out at V. The level is re-applied whenever an anvil or grindstone result is built, so an Efficiency book (which vanilla would cap at V) or a grindstone (which removes enchantments) can't change it. |

- Around 10 already breaks stone and deepslate instantly. Higher values add little for those blocks.
- A Drill made earlier keeps its old level on the item until it goes through an anvil or grindstone, where it is set to the current value.
- Every other pickaxe enchantment goes on through the vanilla anvil rules, `dwellerenchants:unbreakable` included once that plugin exists. This plugin does not limit enchantments.
- Set very high, mining is effectively instant for everything breakable, which makes ore depth matter less.

### hammer

| Key | Default | Unit | Effect |
|---|---|---|---|
| `radius` | `1` | blocks, 1 to 3 | Size of the square around the mined block: `1` = 3x3, `2` = 5x5, `3` = 7x7. It is one block deep, flat against the face the player hit. |
| `skip-harder-blocks` | `true` | boolean | Blocks harder than the mined block are left alone, so mining stone doesn't break obsidian beside it. `false` = every eligible block in the square breaks. |
| `excluded-blocks` | `[]` | list of block ids | Blocks the square never breaks. The block the player mines directly still breaks. Unknown ids log a warning naming the key. |

- A block in the square is only broken if it is in `#minecraft:mineable/pickaxe`, a netherite pickaxe gets a drop from it, and it is not unbreakable (hardness below 0, such as bedrock). `excluded-blocks` and `skip-harder-blocks` apply on top. The three base rules can't be turned off.
- The square is always on. There is no sneak toggle.
- Each extra block goes through the normal break event (`Player#breakBlock`), so [WorldGuard](plugins/worldguard.md) regions, [CoreProtect](plugins/coreprotect.md) logging, Fortune, Silk Touch and XP drops apply to every block. A protected block simply isn't broken.
- Each block, extra ones included, costs 1 durability (Unbreaking can save it). If the tool breaks mid-square the rest of the square is left.
- A large `radius` destroys builds quickly: 7x7 is 48 extra blocks per swing. Protect builds with WorldGuard regions.
- The face comes from the last block the player started mining. A block broken instantly, with no damage phase, reuses the previous face.

### paxel

| Key | Default | Unit | Effect |
|---|---|---|---|
| `mining-speed` | `9` | multiplier, 1 to 100 | Mining speed on every pickaxe, axe and shovel block, before Efficiency and Haste. Vanilla netherite tools are 9. Stored on the item when crafted. |
| `path-sound` | `minecraft:item.shovel.flatten` | sound id | Played when a dirt path is made. `""` = silent. |

- The Paxel is a netherite axe underneath: axe attack damage, axe enchantments, strips logs and scrapes or unwaxes copper by vanilla behaviour.
- Mining rules are a `tool` component on the item: netherite tier and `mining-speed` on `#mineable/pickaxe`, `#mineable/axe` and `#mineable/shovel`, no drops from blocks that need more than netherite. Because it is stored on the item, **changing `mining-speed` only affects new Paxels**, and `enabled: false` does not remove the rules from existing ones.
- Dirt paths: right-click grass block, dirt, podzol, coarse dirt, mycelium or rooted dirt with air above, not on the bottom face. It costs 1 durability. Other plugins can cancel the interaction as for any right-click.

## Interactions

- **[DwellerCore](config-core.md#resource-pack)** sends the resource pack that holds the hook, rope and tool item models. DwellerTools sends nothing itself. If `resource-pack.enabled` is `false`, deliver the pack some other way or players see the base items.
- **[DwellerSurface](config-surface.md)**: the hook works in the surface world unless that world is in `disabled-worlds`.
- **Light blocks and the climbable tag.** Players climb because the plugin moves them every tick (`rope.climb-speed`, `rope.slide-speed`), not because of the tag: some clients never receive the datapack's addition to `#minecraft:climbable` (seen on a Fabric client, where F3 lists no climbable tag on the rope). The tag still makes ropes climbable for mobs and keeps server-side fall checks in line. `minecraft:light` is in the tag only while the `dwellertools` datapack is loaded. A plugin or command that clears light blocks cuts ropes; the plugin notices at its next check and removes the rest of the rope.
- **[WorldGuard](plugins/worldguard.md) and [CoreProtect](plugins/coreprotect.md)**: the plugin places rope blocks itself, not on behalf of the player, so region flags do not stop a rope and CoreProtect does not log it. To keep players from hooking a protected building, put its blocks in `grapple.excluded-blocks` or its world in `disabled-worlds`. Players breaking or placing blocks next to ropes are logged as usual.
- **Mining tools and protection plugins.** The Hammer's extra blocks are broken through the normal break event, so [WorldGuard](plugins/worldguard.md) and [CoreProtect](plugins/coreprotect.md) treat each one as the player's own break. [Veinmine](plugins/veinminer.md) and other break-event plugins see every block too.
- Anyone can climb any rope. Mobs can too, through the climbable tag.
- Creative and spectator players are not exempt, except that a flying player is not moved by a rope.

## Ropes are temporary

A rope is removed:

- `rope.despawn-seconds` after the last player leaves it,
- when its chunk unloads,
- when the anchor block (the hooked block, or the block stood on for a rappel) changes type,
- when anything replaces its top block,
- for every rope, when the plugin is disabled.

Anything that replaces a lower rope block cuts the rope there: that block and everything below go, the part above stays. The owner logging out does nothing. The anchor check compares the block type only, so changing a block state (a different stair shape, for example) does not remove the rope.

### Cleaning up after a crash

Every rope block is recorded in its chunk's persistent data (`dwellertools:rope_blocks`) when placed and removed from it when the rope goes. After a crash, leftover blocks are removed when their chunk loads, but only blocks from that list that are still `minecraft:light` at level 0. Light blocks placed by players or other plugins at other levels are never touched. Until the chunk loads again, a leftover is an invisible climbable block. Rope displays are not saved to disk, and any that slip through are removed when their chunk's entities load.

## Persistent keys

Never rename or remove these. Existing items, entities and chunks carry them.

| Key | On | Meaning |
|---|---|---|
| `dwellertools:grappling_hook` | item | Marks a Grappling Hook. The same name is also used for the recipe and the cooldown group. |
| `dwellertools:rope` | `item_display` entity | Marks rope and hook displays, so leftovers can be removed. |
| `dwellertools:drill`, `dwellertools:hammer`, `dwellertools:paxel` | item | Marks each mining tool. The same names are the recipe keys. |
| `dwellertools:rope_blocks` | chunk | Packed positions of rope blocks, for crash cleanup. |
| `dwellertools:rope_mining` | attribute modifier | The mining speed modifier on `block_break_speed`. |

## Common problems

| Symptom | Cause | Fix |
|---|---|---|
| Holding jump in a rope only makes the player hop | DwellerTools is an older build that relied on the climbable tag, which some clients never receive | Update DwellerTools and restart. |
| Mobs don't climb ropes | The `dwellertools` datapack is not loaded (a `/reload`, or the jar was added to a running server) | Full restart. Check `/datapack list enabled`. |
| Players see a carrot on a stick, string and plain netherite tools instead of the pack models (tripwire hook, chain, netherite pickaxe and axe) | The resource pack was not sent or not applied | See [resource pack troubleshooting](resource-pack.md#troubleshooting). |
| Throws always fail with `The hook can't catch that` | `excluded-blocks`, a face switched off, or the target is not a full solid block | Check `grapple.ceiling`, `wall`, `ledge` and `excluded-blocks`. |
| Log `Config grappling-hook.recipe: ...` | Bad shape or ingredient | Fix it; the default recipe is used meanwhile. |
| Hooks made before a config change behave differently | Name, lore, base item, model, durability and repair material are stored on the item | Only new hooks get the new values. |
| Hammer breaks nothing extra | `hammer.enabled` is `false`, or everything around is excluded, harder, or not a pickaxe block | Check `hammer.enabled`, `excluded-blocks`, `skip-harder-blocks`. |
| Drill Efficiency is not at the configured level | It was made with an older `efficiency-level` | Put it through an anvil or grindstone, or craft a new one. |
| Invisible blocks stay after a crash | The chunk has not been loaded since | They are removed the next time it loads. |

The [DwellerAdmin](config-admin.md#items) panel lists the Grappling Hook, Drill, Hammer and Paxel through `adminItems`. A tool with `enabled: false` is not listed.

## Links

- [DwellerAdmin panel](config-admin.md)
- [DwellerCore resource pack keys](config-core.md#resource-pack)
- [Resource pack](resource-pack.md)
- [Install](install.md)
- [Technical notes](technical.md)
- [MiniMessage format](https://docs.advntr.dev/minimessage/format.html)
- [Paper documentation](https://docs.papermc.io/)

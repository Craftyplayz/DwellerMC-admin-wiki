# DwellerTools config

File: `plugins/DwellerTools/config.yml` (default: `DwellerTools/src/main/resources/config.yml`). Every key sits under `grappling-hook:`.

**When changes apply:** a full **restart**. There is no reload command and the config is read once at startup, recipe and messages included.

**Bad values never stop the server.** A wrong type, a number outside the range, an unknown item or a broken recipe logs a warning that names the key, and the default is used. A key missing from your file uses the default from the jar.

## What it does

DwellerTools holds custom tools. The first is the **Grappling Hook**: right-click throws a hook at a block and a climbable rope drops from it, sneak + right-click rappels down from an edge. Players climb the ropes like ladders. Ropes are temporary and removed by the plugin.

How a rope is built: one `minecraft:light` block (level 0, invisible and not solid) per rope block, made climbable by the plugin's own datapack (it adds `minecraft:light` to `#minecraft:climbable`), plus one `item_display` per block that shows the rope texture, and one `item_display` for the hook at the top. Ropes only ever replace air.

## Install

1. Build: `./gradlew build` in `DwellerTools/`. The jar is `build/libs/DwellerTools-<version>.jar`.
2. Copy it into `plugins/`. Needs Paper 26.3 and Java 25. It is a Paper plugin that loads at `STARTUP` and registers its datapack in the bootstrapper, like DwellerCore.
3. Make sure [DwellerCore](config-core.md#resource-pack) sends the resource pack. Without the pack players see a plain carrot on a stick and string.
4. Restart. The `dwellertools` datapack should show in `/datapack list enabled`.

!!! warning "Restart, never /reload"
    The datapack is registered before worlds load. After a `/reload` or a jar added to a running server, ropes are not climbable until a full restart.

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
| `item.model` | `dwellermc:grappling_hook` | model id | Item model from the [resource pack](resource-pack.md) (`assets/dwellermc/items/grappling_hook.json`). A model the pack does not have shows the base item. |
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
| `rope.model` | `dwellermc:rope` | model id | Item model of one rope block, from the [resource pack](resource-pack.md). |
| `rope.hook-item` | `tripwire_hook` | item id | Vanilla item shown as the hook in flight and at the top of the rope. Not part of the resource pack. |
| `rope.check-ticks` | `5` | ticks, 1 to 20 | How often ropes are checked: who is on them, cut ropes, broken anchors, despawn, mining boost. Lower reacts faster and costs more; despawn and cut timing are rounded to this step. |

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

## Interactions

- **[DwellerCore](config-core.md#resource-pack)** sends the resource pack that holds the hook and rope textures. DwellerTools sends nothing itself. If `resource-pack.enabled` is `false`, deliver the pack some other way or players see the base items.
- **[DwellerSurface](config-surface.md)**: the hook works in the surface world unless that world is in `disabled-worlds`.
- **Light blocks and the climbable tag.** `minecraft:light` is climbable only while the `dwellertools` datapack is loaded. A plugin or command that clears light blocks cuts ropes; the plugin notices at its next check and removes the rest of the rope.
- **[WorldGuard](plugins/worldguard.md) and [CoreProtect](plugins/coreprotect.md)**: the plugin places rope blocks itself, not on behalf of the player, so region flags do not stop a rope and CoreProtect does not log it. To keep players from hooking a protected building, put its blocks in `grapple.excluded-blocks` or its world in `disabled-worlds`. Players breaking or placing blocks next to ropes are logged as usual.
- Anyone can climb any rope, and mobs can too, because the tag is vanilla's climbable tag.
- Creative and spectator players are not exempt.

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
| `dwellertools:rope_blocks` | chunk | Packed positions of rope blocks, for crash cleanup. |
| `dwellertools:rope_mining` | attribute modifier | The mining speed modifier on `block_break_speed`. |

## Common problems

| Symptom | Cause | Fix |
|---|---|---|
| Rope is there but players fall through it | The `dwellertools` datapack is not loaded (a `/reload`, or the jar was added to a running server) | Full restart. Check `/datapack list enabled`. |
| Players see a carrot on a stick and string instead of the textures | The resource pack was not sent or not applied | See [resource pack troubleshooting](resource-pack.md#troubleshooting). |
| Throws always fail with `The hook can't catch that` | `excluded-blocks`, a face switched off, or the target is not a full solid block | Check `grapple.ceiling`, `wall`, `ledge` and `excluded-blocks`. |
| Log `Config grappling-hook.recipe: ...` | Bad shape or ingredient | Fix it; the default recipe is used meanwhile. |
| Hooks made before a config change behave differently | Name, lore, base item, model, durability and repair material are stored on the item | Only new hooks get the new values. |
| Invisible blocks stay after a crash | The chunk has not been loaded since | They are removed the next time it loads. |

## Links

- [DwellerCore resource pack keys](config-core.md#resource-pack)
- [Resource pack](resource-pack.md)
- [Install](install.md)
- [Technical notes](technical.md)
- [MiniMessage format](https://docs.advntr.dev/minimessage/format.html)
- [Paper documentation](https://docs.papermc.io/)

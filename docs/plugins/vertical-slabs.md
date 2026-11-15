# Vertical slabs (vslab + DefCoreLib)

**vslab 0.5.0** adds vertical slabs and sideways stairs. It is a thin plugin: it only declares `depend: [DefCoreLib]`, and all content comes from **DefCoreLib 0.5.0**, a shared library by def9a2a4 that builds custom blocks out of player heads and display entities. Both jars must be in `plugins/`. Player page: [Vertical slabs](https://craftyplayz.github.io/DwellerMC-wiki/plugins/vertical-slabs/).

## What players get
`slabs.yml` (namespace `verticalslabs`) defines **119 blocks**: 61 vertical slabs (every wood, stone, brick, copper, deepslate, tuff, blackstone, quartz, prismarine, purpur, sandstone and similar slab in the game) and 58 "sideways stairs".

- Recipe: put **one slab** (or stairs) alone in a crafting grid (shapeless) to get the vertical version. The recipe ids are `<name>_vslab_toggle` and `<name>_vstairs_toggle`.
- Placement: click the **side** of a block (north, south, east or west faces). Direction follows the clicked face; stairs also pick left or right.
- Drops: the block drops itself. Breaking and placing play the matching vanilla sounds.
- Appearance: a head block (invisible texture `slab_placeholder`) with a `block_display` entity showing the real slab or stairs model rotated upright, so textures always match vanilla.
- Name: `&fOak Vertical Slab`, `&fOak Sideways Stairs`, and so on.

## DwellerMC notes
- The blocks are **real player-head blocks plus display entities**. WorldEdit does not copy the display entities, so pasting or moving a build with WorldEdit leaves bare head blocks. DefCoreLib has `/defcorelib cleanorphans` for display entities left without a block.
- Fluids, pistons, fire and explosions have handlers in DefCoreLib, so blocks do not leave orphans in normal play.
- Display entities do count toward entity limits and client load. Large builds made only of vertical slabs are heavier than normal blocks. Watch entities in the busy cave rooms.
- Nothing in vslab depends on the surface, sky or Y range.
- DefCoreLib also contains other content (rotation/mechanical blocks, carts, mills, sieves, a block catalog, demo and showcase blocks). Those pieces are built for companion plugins by the same author. Only vslab is installed here, so players will mostly see the catalog command and vertical slabs.

## Commands
`/defcorelib` has no command-level permission. Each subcommand checks its own node.

| Subcommand | Permission | Effect |
|---|---|---|
| `catalog [search]` | `corelib.catalog` (default true) | Open the item catalog GUI and search it. |
| `give` | `corelib.admin` | Give a custom block or item by id. |
| `give_demo`, `list`, `colliders`, `reloadbanners`, `cleanorphans [confirm]` | `corelib.admin` | Testing and maintenance tools. |

Run `/defcorelib` for the usage line.

## Permissions
| Node | Default | Effect |
|---|---|---|
| `corelib.admin` | op | Everything except the catalog; includes `corelib.catalog`. |
| `corelib.catalog` | **true** | Browse the catalog. |

Crafting the vertical blocks needs no permission. Recommended default group: nothing to add. To keep players from using the catalog: `lp group default permission set corelib.catalog false`.

## Config and data
Neither plugin ships a `config.yml`. Content is bundled in the DefCoreLib jar (`slabs.yml`, `rotation-config.yml`, `carts-config.yml`, ...) and is not meant to be edited.

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| vslab does not load | DefCoreLib missing or a different version | Install both 0.5.0 jars. |
| Invisible or bare head blocks | Display entity removed (WorldEdit, `/kill @e`, entity-clearing plugins) | Mine the head and place it again; run `/defcorelib cleanorphans`. |
| Blocks drop nothing | Block broken by something that bypasses events | Rare; place a new one. |

Back to the [admin index](../index.md) or [troubleshooting](../troubleshooting.md).

## Links
- Search "DefCoreLib" or "VerticalSlabs" by def9a2a4 on Modrinth or Spigot.

# WorldEdit

| | |
|---|---|
| Version | 7.4.6 (build 7648-00664b0) |
| Jar | `worldedit-bukkit-7.4.6.jar` |
| Load | `STARTUP` |
| Soft depends | Vault (not installed) |
| Data folder | `plugins/WorldEdit/` (`config.yml`, `schematics/`, `sessions/`, `assets/`) |
| Folia | supported |

WorldEdit is the in-game building tool: select an area, then fill, replace, copy, paste, undo. WorldGuard needs it to define regions.

!!! note "No player can use it until you give permissions"
    Ops can use everything (unless `no-op-permissions: true`). Players have nothing by default. Give nodes with [LuckPerms](luckperms.md).

## Using it on DwellerMC

- The world is 2048 blocks tall (Y -2032 to 15). WorldEdit reads the height from the world, so selections can span the full height.
- `//expand vert` stretches a selection to the top and bottom of the world. This is how you make WorldGuard regions that cover every depth.
- `limits.vertical-height` (default `256`) is the height used by commands with an optional height, such as `/removeabove`. It is not a world limit.
- `//set`, `//replace` and friends on thousands of blocks can lag a small server. Set a block limit (see below).
- There is no surface. `/ascend`, `/descend`, `/thru` and `/jumpto` work inside caves and are useful for moving around. `/up` places glass under you when in mid-air.
- `/unstuck` moves you to the nearest free spot above. It can move a player up a long way, so keep it staff only. EssentialsX `/top` is disabled on this server for the same reason.
- `//regen` regenerates a region with the world generator. On this world that means DwellerCore's generator; do not use it on important builds. Try it on a copy first.
- Mob level scaling: WorldEdit does not touch mobs, except `/butcher` and `/remove`, which delete them (leveled mobs and elites included).
- Bedrock is in the default `disallowed-blocks`, so non-bypass users cannot place it in patterns.

## Commands

Most WorldEdit commands start with `//` (two slashes). Use `/` + `/command` in chat. Permission nodes follow `worldedit.<category>.<command>`.

### General

| Command | Does | Permission |
|---|---|---|
| `//undo [times] [player]`, `//redo` | History | `worldedit.history.undo`, `.redo` |
| `//clearhistory` | Clear your history | `worldedit.history.clear` |
| `//limit [n]` | Your max blocks per edit | `worldedit.limit` |
| `//timeout [ms]` | Expression time limit | `worldedit.timeout` |
| `//perf`, `//fast` | Speed and side-effect settings | `worldedit.perf`, `worldedit.fast` |
| `//gmask [mask]` | Global mask | `worldedit.global-mask` |
| `//searchitem` | Search block/item names | `worldedit.searchitem` |
| `//help`, `//reload`, `//version`, `//report`, `//tz`, `//cui` | Help and admin | `worldedit.help`, `.reload`, `.report` |

### Selection

| Command | Permission |
|---|---|
| `//wand [-n]`, `//pos1`, `//pos2`, `//pos`, `//hpos1`, `//hpos2`, `//chunk`, `//sel [type]` | `worldedit.wand`, `worldedit.selection.pos`, `.hpos`, `.chunk` |
| `//expand`, `//contract`, `//shift`, `//outset`, `//inset`, `//trim`, `//size` | `worldedit.selection.expand`, `.contract`, `.shift`, `.outset`, `.inset`, `.trim`, `.size` |
| `//count <mask>`, `//distr` | `worldedit.analysis.count`, `.distr` |

Selection types for `//sel`: `cuboid`, `extend`, `poly`, `ellipsoid`, `sphere`, `cyl`, `convex`.

### Region editing

| Command | Permission |
|---|---|
| `//set <pattern>` | `worldedit.region.set` |
| `//replace [from] <to>` | `worldedit.region.replace` |
| `//overlay`, `//walls`, `//faces`, `//line`, `//smooth`, `//hollow`, `//move`, `//stack` | `worldedit.region.<name>` |
| `//regen [-bc] [seed]` | `worldedit.regen` |
| `//generate`, `//sphere`, `//hsphere`, `//cyl`, `//pyramid`, `//feature`, `//structure` | `worldedit.generation.*` |
| `//fill`, `//fillr`, `//drain`, `/fixwater`, `/fixlava` | `worldedit.fill`, `.fill.recursive`, `.drain`, `.fixwater`, `.fixlava` |
| `/removeabove`, `/removebelow`, `/removenear`, `/replacenear` | `worldedit.removeabove` etc. |
| `/butcher [-abfgnprtw] [radius]`, `/remove <type> <radius>` | `worldedit.butcher`, `worldedit.remove` |
| `//setbiome [-p] <biome>`, `//biomelist`, `//biomeinfo` | `worldedit.biome.*` |

### Clipboard and schematics

| Command | Permission |
|---|---|
| `//copy [-be] [-m mask]`, `//cut`, `//paste [-abenosv]`, `//rotate`, `//flip`, `/clearclipboard` | `worldedit.clipboard.copy`, `.cut`, `.paste`, `.rotate`, `.flip`, `.clear` |
| `/schematic list`, `load`, `save`, `delete`, `formats`, `share` (alias `/schem`) | `worldedit.schematic.*` and `worldedit.clipboard.load`/`.save` |

Schematics live in `plugins/WorldEdit/schematics/`. See [DwellerStructures](../config-structures.md) for the server's own structures; those are separate files.

### Navigation

`/unstuck` (`worldedit.navigation.unstuck`), `/ascend`, `/descend`, `/ceil`, `/thru`, `/jumpto`, `/up` (`worldedit.navigation.<name>`; `jumpto` and `thru` also have `.tool` variants for the wand).

### Tools and brushes

| Command | Permission |
|---|---|
| `/tool`, `/tool selwand`, `/tool navwand`, `/tool repl <pattern>`, `/tool none` | `worldedit.tool.*`, `worldedit.setwand` |
| `//` (toggle super pickaxe), `/superpickaxe area <r>`, `/superpickaxe recursive <r>` | `worldedit.superpickaxe`, `.area`, `.recursive` |
| `/brush <shape> ...`, `/mask`, `/material`, `/range`, `/size`, `/tracemask` | `worldedit.brush.*`, `worldedit.brush.options.*` |

### Other

`/restore`, `/snapshot list` (`worldedit.snapshots.*`), `/cs <script>` (`worldedit.scripting.execute`), `//calc` (`worldedit.calc`), `/chunkinfo`, `/listchunks`, `/delchunks` (`worldedit.chunkinfo`, `.listchunks`, `.delchunks`), `//snow`, `//thaw`, `//green`, `//extinguish` (`/ex`).

### Special permission nodes

| Node | Effect |
|---|---|
| `worldedit.*` | Everything |
| `worldedit.limit.unrestricted` | Ignore block limits and raise `//limit` |
| `worldedit.timeout.unrestricted` | Ignore the expression timeout |
| `worldedit.anyblock` | Use blocks in `disallowed-blocks` |
| `worldedit.override.bedrock` | Break bedrock with the super pickaxe |
| `worldedit.setnbt` | Set block entity data (sign text, chest contents) |
| `worldedit.inventory.unrestricted` | Ignore `use-inventory` |
| `worldedit.scripting.execute.<file>` | Run one CraftScript |
| `worldedit.report.pastebin` | Upload `//report` |

## Configuration: `plugins/WorldEdit/config.yml`

The file on the test server is identical to the one in the jar.

### Limits

| Key | Default | Effect |
|---|---|---|
| `limits.max-blocks-changed.default` | `-1` | Blocks per edit for normal users. `-1` = no limit. |
| `limits.max-blocks-changed.maximum` | `-1` | Highest value `//limit` can set. |
| `limits.vertical-height.default` | `256` | Height for commands with an optional height. |
| `limits.max-polygonal-points.default` / `maximum` | `-1` / `20` | Points for `//sel poly` and `convex`. |
| `limits.max-radius` | `-1` | Largest radius for radius commands. |
| `limits.max-super-pickaxe-size` | `5` | Super pickaxe size. |
| `limits.max-brush-radius` | `5` | Brush size. |
| `limits.butcher-radius.default` / `maximum` | `-1` / `-1` | `/butcher` radius. |
| `limits.disallowed-blocks` | list | Blocks banned from patterns: saplings, beds, rails (powered, detector, plain), short grass, dead bush, piston parts, flowers, mushrooms, TNT, torch, fire, redstone wire/torch, crops, buttons, lever, repeater, comparator, cactus, sugar cane, bedrock. `worldedit.anyblock` bypasses. |

### Behaviour

| Key | Default | Effect |
|---|---|---|
| `use-inventory.enable` | `false` | Edits take blocks from the player's inventory. Leave off. |
| `use-inventory.allow-override` | `true` | `worldedit.inventory.unrestricted` can bypass. |
| `use-inventory.creative-mode-overrides` | `false` | Creative mode bypasses. |
| `logging.log-commands` | `false` | Log commands to `logging.file`. |
| `logging.file` | `worldedit.log` | Log file. |
| `logging.format` | `[%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS %4$s]: %5$s%6$s%n` | Java format string. |
| `super-pickaxe.drop-items` | `true` | Single mode drops items. |
| `super-pickaxe.many-drop-items` | `false` | Area modes drop items. |
| `snapshots.directory` | empty | Backup folder for `/restore`. |
| `navigation-wand.item` | `minecraft:compass` | Item for `/jumpto` and `/thru`. |
| `navigation-wand.max-distance` | `100` | Trace distance. |
| `scripting.timeout` | `3000` | CraftScript time limit (ms). |
| `scripting.dir` | `craftscripts` | CraftScript folder. |
| `saving.dir` | `schematics` | Schematic folder. |
| `files.allow-symbolic-links` | `false` | |
| `history.size` | `15` | Undo steps kept per player. |
| `history.expiration` | `10` | Minutes before history is dropped. |
| `calculation.timeout` | `100` | Expression time limit (ms). |
| `debugging.trace-unflushed-sessions` | `false` | |
| `wand-item` | `minecraft:wooden_axe` | Selection wand. |
| `shell-save-type` | empty | |
| `no-op-permissions` | `false` | If `true`, ops get nothing automatically. |
| `debug` | `false` | |
| `show-help-on-first-use` | `true` | Tell a new user how to use the wand. |
| `server-side-cui` | `true` | Allow `//drawsel`. |
| `command-block-support` | `false` | Let command blocks run WorldEdit commands. |

## Data files

| Path | What |
|---|---|
| `plugins/WorldEdit/config.yml` | Config |
| `plugins/WorldEdit/schematics/` | Saved schematics (`.schem`) |
| `plugins/WorldEdit/sessions/` | Per-player saved sessions (selection, tool settings) |
| `plugins/WorldEdit/worldedit.log` | Only if `log-commands` is on |

## Interactions

- **WorldGuard:** WorldGuard hooks WorldEdit. Players can only edit areas they could build in without WorldEdit, unless they have bypass. See [WorldGuard](worldguard.md).
- **CoreProtect:** logs WorldEdit edits when its `worldedit` option is on (default). Roll them back with `r:#worldedit`. See [CoreProtect](coreprotect.md).
- **LuckPerms:** `wepif.yml` in the server root is only for old permission plugins; with LuckPerms, WorldEdit uses normal Bukkit permission checks.
- **DwellerCore:** `//butcher` and `/remove` delete leveled mobs without drops. `//regen` uses the world's generator.

## Recommended for this server

1. Only staff get WorldEdit. Suggested admin set: `worldedit.*`. Suggested builder set: `worldedit.wand`, `worldedit.selection.*`, `worldedit.region.*`, `worldedit.history.*`, `worldedit.clipboard.*`, `worldedit.limit` plus a lower limit.
2. Set `limits.max-blocks-changed.default` to something like `200000` and `maximum` to `1000000`. Staff with `worldedit.limit.unrestricted` ignore it. This avoids a very large edit freezing the server (tune with [spark](spark.md)).
3. Set `limits.max-radius` and `limits.butcher-radius.maximum` to `100`.
4. Turn on `logging.log-commands` if several staff use it.
5. Keep `use-inventory.enable: false`.

## Common problems

| Problem | Fix |
|---|---|
| "You are not allowed to use that command" | Missing node. Ops need `no-op-permissions: false`. |
| `//set` stops at "max blocks changed" | Raise `//limit` (needs `worldedit.limit`). |
| Selection is only part of the height | `//expand vert`. |
| "Block is not allowed" | In `disallowed-blocks`. Remove it from the list or grant `worldedit.anyblock`. |
| Big edit lags the server | Lower the limit, split the selection, or use `//perf`. |

## Links

- [WorldEdit docs](https://worldedit.enginehub.org/en/latest/)
- [Commands](https://worldedit.enginehub.org/en/latest/commands/)
- [Permissions](https://worldedit.enginehub.org/en/latest/permissions/)
- [Configuration](https://worldedit.enginehub.org/en/latest/config/)

Related: [Index](../index.md), [WorldGuard](worldguard.md), [CoreProtect](coreprotect.md), [LuckPerms](luckperms.md).

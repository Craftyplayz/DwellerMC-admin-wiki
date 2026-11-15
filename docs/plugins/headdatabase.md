# HeadDatabase

**Version 4.21.1-mc26.3** (Arcaniax, premium plugin on Spigot, resource 14280). A menu of decorative heads from minecraft-heads.com. This server runs a **patched build for Minecraft 26.3**; the patch notes and source for the patch live in `d:/HDD/backup/!Git/headdatabase/`.

Players do **not** get access by default (see below), so there is no player page.

## The 26.3 patch
Seven small bytecode changes and one replaced helper class (`HeadUtil`):
- The version parser now accepts "26.3" style versions (regex `MC: (?:\d\.)?(\d+)`).
- Player heads use Paper's `PlayerProfile` API instead of NMS reflection.
- `GameProfile` access in `HeadDatabaseAPI` uses the record accessors `name()`, `properties()`, `value()` of newer authlib.
- Branches for servers older than 1.21.9 were removed. If you ever downgrade the server, use the original jar instead.

## Needs
- Internet access: the head list is downloaded from the plugin's database host on start and refreshed every `poll-rate` minutes. If it cannot connect it retries `attempt-amount` times.
- Optional softdepends: Vault, PlayerPoints (economy), PlaceholderAPI, Geyser-Spigot.

## Commands
Aliases: `/headdb`, `/hdb`, `/heads` and `/playerhead`, `/phead`, `/playerh`.

| Command | Permission | Effect |
|---|---|---|
| `/hdb` | `headdb.open` | Open the menu. |
| `/hdb open [player] [category/search]` | `headdb.open` (others: `headdb.admin`) | Open for yourself or another player, optionally on a category or a search. |
| `/hdb search <text>` | `headdb.open` | Search by name or tag. |
| `/hdb info` | `headdb.open` | Version, head count, economy state. |
| `/hdb reload` | `headdb.admin` | Reload config and database. |
| `/hdb give`, `random`, `debug`, `base64` | `headdb.admin` | Give a head by id, a random head, debug output, a head from a texture string. |
| `/phead <name>` | `headdb.phead` | Get a player's head. |

## Permissions
| Node | Default | Effect |
|---|---|---|
| `headdb.open` | op | Open the menu. |
| `headdb.phead` | op | `/phead`. |
| `headdb.category.*` | true | See categories in the menu. Per category nodes `headdb.category.<name>` exist (alphabet, animals, blocks, decoration, food_drinks, humans, humanoid, miscellaneous, monsters, plants, custom, online_players). `headdb.category.all` is false. |
| `headdb.allow.buy.*` | op | Take heads. Per category: `headdb.allow.buy.<category>`. |
| `headdb.free.*`, `headdb.free.all`, `headdb.free.<category>`, `headdb.free.phead` | false | Take heads without paying (matters only with economy on). |
| `headdb.online.hide` | op | Hide yourself from the online-players category. |
| `headdb.admin` | op | Reload and admin subcommands. |
| `headdb.admin.showid` | op | Show head ids in lore. |
| `headdb.admin.nocooldown` | op | Skip the head cooldown. |
| `headdb.admin.disabled` | false | See heads listed in `disabled-heads`. |

Players hold none of `headdb.open`, `headdb.phead` or `headdb.allow.buy.*` by default, so the plugin does nothing for them.

### Recommendation
Keep it staff-only (builders and admins). For a trusted builder group:

```
/lp creategroup builder
/lp group builder permission set headdb.open true
/lp group builder permission set headdb.allow.buy.* true
```

If you want it for everyone as a free cosmetics menu, give the same two nodes to `default` and leave `Economy-Enabled: false`. With a large head menu, players can get rude or unwanted heads, so consider also keeping some categories in `disabled-categories` and using `disabled-heads` tags.

## Config (`plugins/HeadDatabase/config.yml`)
`/hdb reload` applies most keys; heads and categories listed in the disabled lists are removed on database update or restart.

### Language and messages
| Key | Default | Effect |
|---|---|---|
| `locale` | `en_US` | Language file name from the `texts/` folder (also `de_DE, fr_FR, es_ES, nl_NL, ...`). |
| `show-permission` | `true` | Tell players which permission they lack. |
| `drop-overflow-heads-message` | `false` | Message when a head drops because the inventory is full. |

### Connection
| Key | Default | Effect |
|---|---|---|
| `attempt-amount` | `10` | Connection attempts. |
| `poll-rate` | `360` | Minutes between update checks. |

### Behavior
| Key | Default | Effect |
|---|---|---|
| `cooldown-heads` | `false` | Cooldown between taking heads. |
| `cooldown-time` | `3` | Seconds. |
| `drop-overflow-heads` | `false` | Drop heads on the ground if the inventory is full. |
| `keep-category` | `false` | Keep the category in the head lore. |
| `use-chat-search` | `false` | Search by typing in chat; if false, the player is asked to use the command. |
| `return-name-on-pickup` | `true` | Restore name and lore when the placed head is picked up. |
| `experimental-loading`, `loadHeadAmount`, `loadTimeDelay` | `true`, `9`, `10.0` | Async head loading (disabled automatically on 1.13+). |
| `remove-invalid-heads` | `false` | Remove broken HD heads from inventories. |
| `disabled-heads` | `TAG:Alcohol` | Heads removed (by name, `ID:<n>`, or `TAG:<tag>`). The default hides alcohol heads. |
| `disabled-categories` | `""`, `online_players`, `custom2` to `custom5` | Categories hidden. |
| `rename-heads` | `12105: "Iron Armour Herobrine"` | Rename by id. |

### Menu layout
| Key | Default | Effect |
|---|---|---|
| `<category>-id` | e.g. `alphabet-id: 164` | Head id used as the category icon. |
| `show-no-permission-search` | `false` | Show unobtainable heads in search. |
| `info-enabled`, `suggest-enabled`, `search-enabled`, `command-enabled`, `featured-tags-enabled` | `true`, `true`, `true`, `false`, `true` | Buttons in the main menu. |
| `main-menu-category-slots` | `10,11,12,13,14,15,16,19,...,34` | Category slots. |
| `main-menu-info-slot`, `-suggest-slot`, `-search-slot`, `-command-slot` | `41`, `42`, `43`, `49` | Button slots. |
| `arrow-menu-nav-enabled`, `arrow-menu-search-enabled`, `arrow-menu-current-page-enabled` | `false`, `true`, `true` | Arrow navigation style. |
| `back-to-menu-slot`, `arrow-back-slot`, `current-page-slot`, `arrow-next-slot`, `arrow-search-slot` | `45, 47, 49, 51, 53` | Arrow style slots. |
| `page-menu-switch-slots` | `false` | Swap search and back buttons. |
| `arrow-item`, `paper-page-item`, `current-page-item`, `back-to-menu-item`, `info-item`, `suggest-item`, `search-item`, `command-item`, `background-item`, `background-border-item`, `loading-head-item` | arrow, paper, empty_map, book, oak_sign, writeable_book, compass, barrier, light_gray_stained_glass_pane, gray_stained_glass_pane, player_head | Menu items (must all differ). |
| `custom-command` | `menu` | Command run by the command item (a back button to another menu). |
| `main-menu-featured-tags-slots` | `37,...,43` | Featured tag slots. |
| `featured-tags` | list | Seasonal rows: `title:ids:search:start:end`. Halloween, Christmas, New Year, Valentine's, 4th of July, Easter, winter, summer, May the 4th, Pi day, and more. |

### Economy
| Key | Default | Effect |
|---|---|---|
| `Economy-Enabled` | `false` | Charge for heads. |
| `Economy-Currency` | `$` | Symbol on heads. |
| `Economy-Type` | `currency` | `item`, `currency` (Vault) or `playerpoints`. |
| `Economy-Item`, `-ItemName`, `-NoNamedItems`, `-ItemLore`, `-CustomModelData` | `gold_ingot`, `""`, `false`, `""`, `-1` | Settings for `item` type. |
| `close-inventory-on-failed-purchase`, `close-inventory-on-successful-purchase` | `false`, `false` | Close the menu. |
| `<category>-price` | 100 (blocks 200, custom 1000) | Price per head. `phead-price: 100`. |

Vault is not installed here, so the `currency` type has no provider. Leave economy off, or use the `item` type.

### Custom categories
`custom`, `custom2` to `custom5` map a name to a texture, `uuid:<uuid>`, `vanilla:<mob>`, `personal_head` or `id:<n>`, optionally with `;price`. Do not use commas in names. `custom2` (player and mob skulls, with prices) and `custom3` to `custom5` are in `disabled-categories` by default.

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| Menu empty or "database not loaded" | No internet, host down | Check connectivity and retry; look at the console. |
| Heads show as Steve | Skin server unreachable or client cache | Restart the client. |
| Plugin fails with a version error | Wrong jar (unpatched build) | Use the 26.3 patched jar. |
| Players cannot open `/hdb` | Missing `headdb.open` | Grant it per the recommendation. |
| "You are lacking the permission node" when taking a head | Missing `headdb.allow.buy.<category>` | Grant it. |

Back to the [admin index](../index.md) or [troubleshooting](../troubleshooting.md).

## Links
- [HeadDatabase on Spigot](https://www.spigotmc.org/resources/head-database.14280/)
- [Minecraft-Heads](https://minecraft-heads.com/)

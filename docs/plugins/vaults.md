# Vaults

**Version 2.2.1** (Breiwalker). Personal, multi-page storage that opens with `/vault`. Contents follow the player, can be shared with other online players, and live in a SQLite database.

Player page: [Vaults](https://craftyplayz.github.io/DwellerMC-wiki/plugins/vaults/).

## How it works
- Each player has one vault of `vault-settings.total-pages` pages. Each page is an inventory with `vault-settings.rows` rows (default 6 rows, 2 pages). Next/Previous buttons switch pages.
- Page contents are saved as binary blobs in `plugins/Vaults/vaults.db` and loaded when opened.
- `/vault share <player>` gives an **online** player access to your vault. The guest opens it with `/vault <owner>`. Shares persist until removed.
- Vaults are not tied to a location, so there are no chests to protect, steal or blow up. Items inside are safe from death and mob damage.
- On the first start after upgrading from an old YAML version, the plugin migrates `userdata` files into the database and renames the folder `userdata.backup`.
- SQLite JDBC is declared as a `libraries:` entry in `plugin.yml`, so Paper downloads it from Maven Central on first start. The server needs internet access once. It is cached under `libraries/`.

## Commands
| Command | Who | Effect |
|---|---|---|
| `/vault` | everyone | Open your own vault (page 1). |
| `/vault share <player>` | everyone | Give an online player access. |
| `/vault unshare <player>` | everyone | Remove access. |
| `/vault shared` | everyone | List who can open your vault. |
| `/vault <player>` | everyone with a share | Open that player's vault. |
| `/vaultadmin` (`/va`) | `vaults.admin` | Admin tools, below. |

`/vault` has no permission node in `plugin.yml`, so **every player can use it by default**.

### `/vaultadmin`
| Subcommand | Effect |
|---|---|
| `open <player> [page]` | Open any player's vault (player only, not console). |
| `shares <player>` | Show who they share with and who shares with them. |
| `unshare <owner> <guest>` | Remove a share. |
| `clear <player> [page\|all]` | Delete a page, or all pages. `all` needs `confirm` at the end: `/vaultadmin clear <player> all confirm`. |
| `reload` | Reload `config.yml`. |
| `info <player>` | Page count, storage size (KB), shares out and in. |

## Permissions
| Node | Default | Effect |
|---|---|---|
| `vaults.admin` | op | `/vaultadmin` and everything under it. |
| `vaults.update` | op | Join notice when a new version exists (the node name is configurable). |

No other permissions exist. There is no node to limit who may use `/vault`; use a command-blocker plugin if you need that.

Recommended default group: nothing to add.

## Config (`plugins/Vaults/config.yml`)
| Key | Default | Effect |
|---|---|---|
| `config-version` | `3` | Internal. Do not edit. |
| `vault-settings.rows` | `6` | Rows per page, 1 to 6. |
| `vault-settings.total-pages` | `2` | Pages per vault. |
| `vault-settings.title` | `Vault %page%` | Inventory title. |
| `update-checker.enabled` | `true` | Ask Modrinth for newer versions. |
| `update-checker.channel` | `release` | `release`, `beta`, `alpha` or `all`. |
| `update-checker.notify-console` | `true` | Log a notice. |
| `update-checker.notify-ops-on-join` | `true` | Tell players with the permission. |
| `update-checker.notify-permission` | `vaults.update` | Permission for the join notice. |
| `messages.*` | see file | All chat texts, `&` color codes. `prefix` is `&8[&6Vaults&8] `. Placeholders: `%player%`, `%page%`, `%count%`, `%kb%`, `%pages%`, `%guests%`, `%owners%`, `%owner%`, `%guest%`, `%version%`, `%current%`. Navigation button names (`nav-next`, `nav-prev`) get no prefix. |
| `telemetry.enabled` | `true` | Send an anonymous report to a public relay. |
| `telemetry.endpoint` | a `workers.dev` URL | Relay address. Blank means nothing is sent. |
| `telemetry.heartbeat-minutes` | `360` | Report interval. |
| `telemetry.report-errors` | `true` | Include Vaults' own errors. |

`/vaultadmin reload` applies config changes. Do **not** lower `rows` or `total-pages` once players have items in the pages that would disappear; export or empty them first.

### Privacy
Telemetry is separate from bStats. According to the file comments it does not send IP addresses, player names or UUIDs. A random install id is stored in `plugins/Vaults/telemetry.state`. If you do not want any outside calls, set `telemetry.enabled: false` and `update-checker.enabled: false`. bStats is controlled in `plugins/bStats/config.yml`.

## Data
`plugins/Vaults/vaults.db` (SQLite, WAL mode, so `vaults.db-wal` and `vaults.db-shm` appear next to it).

| Table | Content |
|---|---|
| `vault_pages` | `owner_uuid`, `page`, `data` (serialized items), `updated_at` |
| `vault_shares` | `owner_uuid`, `guest_uuid`, `created_at` |
| `meta` | Internal flags such as `yaml_migrated` |

Back up by stopping the server and copying all three `vaults.db*` files, or use SQLite's backup tools while it runs.

## Interactions
- **CoreProtect**: does not log vault inventories. Use `/vaultadmin info` and `open` for investigations.
- **InvSee++ / Essentials**: do not see vault contents.
- **BreweryX**: brews can be stored in vaults like any item.
- **DwellerCore**: nothing special. Mob loot goes to the player's inventory as usual, so vaults replace ender chests as the safe store.

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| "Failed to load vault" | Database locked or unreadable | Check the console and file permissions on `vaults.db*`. |
| Plugin disables on first start | SQLite library could not be downloaded | Allow internet for the first start. |
| Share command says player is offline | Sharing needs the target online | Ask them to join. |
| Items missing after lowering pages/rows | Settings reduced | Restore the old values. |

Back to the [admin index](../index.md) or [troubleshooting](../troubleshooting.md).

## Links
- [Vaults on Modrinth](https://modrinth.com/plugin/vaults)

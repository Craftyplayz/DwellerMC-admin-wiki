# Resource pack

The server has one required resource pack. It holds the item models for custom items (the [Grappling Hook](config-tools.md) and its rope, and the Drill, Hammer and Paxel) and one sound change: the totem sound is muted so [DwellerEnchants](config-enchants.md) can play its attune animation silently. It lives in its own public repo, `DwellerMC-resourcepack` (GitHub: Craftyplayz), and [DwellerCore](config-core.md#resource-pack) sends it to every player on join.

## Fixed links

GitHub Actions rebuilds the pack on every push to `main` and uploads it to one release that is never renamed, tagged `pack`:

- Pack: `https://github.com/Craftyplayz/DwellerMC-resourcepack/releases/download/pack/DwellerMC.zip`
- SHA-1: `https://github.com/Craftyplayz/DwellerMC-resourcepack/releases/download/pack/DwellerMC.zip.sha1`

The links never change, so the server config never needs editing for a pack update.

## How the server uses it

1. At startup DwellerCore downloads the `.sha1` file (in the background, with redirects followed) and keeps the 40-character hash.
2. On every join it sends the pack with that hash. The client uses the hash to know whether its cached copy is current.
3. A player who declines the pack, or whose download fails or has an invalid link, is kicked with `resource-pack.kick-message`. This only happens while `resource-pack.required` is `true`.

So **a pack update only needs a server restart**: push to the repo, wait for the Action to finish (about a minute), then restart. Players get the new pack the next time they join. If the hash could not be downloaded, the log says so (`Resource pack: could not download the SHA-1 ...`), the pack is still sent without a hash and players may keep an old copy. A player who joins in the first moments after startup, before the background download finishes, also gets the pack without a hash.

Keep `resource-pack` in `server.properties` **empty**. DwellerCore replaces it, and a second pack would be sent as well.

## Repo layout

| Path | What it is |
|---|---|
| `pack.mcmeta`, `pack.png` | Pack metadata (pack format 97 for Minecraft 26.3, `min_format` and `max_format`) and icon. |
| `assets/dwellermc/items/` | Item model definitions (`grappling_hook.json`, `rope.json`, `drill.json`, `hammer.json`, `paxel.json`). The names are the model ids used in config: `dwellermc:grappling_hook` is `item.model` and `dwellermc:rope` is `rope.model` in [DwellerTools](config-tools.md), and `dwellermc:drill`, `dwellermc:hammer` and `dwellermc:paxel` are the `item.model` of the mining tools. The json files currently point at vanilla models: `grappling_hook` at `minecraft:item/tripwire_hook`, `drill` and `hammer` at `minecraft:item/netherite_pickaxe`, `paxel` at `minecraft:item/netherite_axe` and `rope` at `minecraft:block/chain`. The pack has no custom textures at the moment. |
| `assets/dwellermc/models/`, `assets/dwellermc/textures/` | Not present now. Create them when you add custom art (see below). |
| `assets/minecraft/sounds.json` | [DwellerEnchants](config-enchants.md): mutes `item.totem.use`, so the attune animation (a totem animation) is silent. Real totems are silent too. |
| `.github/workflows/release.yml` | Builds and uploads the zip. |

## Adding a texture or model

1. Put the texture in `assets/dwellermc/textures/item/` (or `block/`).
2. Add a model in `assets/dwellermc/models/item/` that points at it.
3. Add or edit the item definition `assets/dwellermc/items/<name>.json`. Its file name is the model id (`dwellermc:<name>`). To replace a vanilla placeholder, change the `model` in the existing json from the vanilla path to your new model (for example `dwellermc:item/<name>`).
4. Point the plugin config at that id (for example `grappling-hook.item.model`). A config change needs a plugin restart as well.
5. Commit and push to `main`. When the Action is green, restart the server.

After a Minecraft update, raise `min_format` and `max_format` in `pack.mcmeta` to the new pack format.

## Release flow

`release.yml` runs on every push to `main` and on manual dispatch:

1. `zip -rX DwellerMC.zip pack.mcmeta pack.png assets` (no extra attributes, so the zip is not polluted by file times).
2. `sha1sum` writes the hash to `DwellerMC.zip.sha1`.
3. The `pack` release is created the first time and both files are uploaded with `--clobber`, replacing the old ones.

To rebuild without a change, run the workflow manually (the Actions tab, "Release pack").

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Players are kicked on join | They declined the pack, or the download failed | They set Server Resource Packs to Enabled in the server list (Edit) and rejoin. Check that the pack link opens in a browser. |
| Everybody is kicked | The pack URL is wrong, or the release is private or gone | Fix `resource-pack.url` or restore the `pack` release. Set `required: false` meanwhile. |
| Log: `Resource pack: ... returned HTTP 404 without a SHA-1` | The release or the `.sha1` file does not exist yet | Run the workflow. The pack is sent without a hash until then. |
| Updated pack not showing | Server not restarted, or the hash could not be read | Restart and check the log for `Resource pack SHA-1: ...`. |
| Items show as carrot on a stick, string or plain netherite tools | The pack was not applied, or the model id in the config does not exist in the pack | Check the ids against `assets/dwellermc/items/`. |
| Two packs download | `resource-pack` is set in `server.properties` | Empty it. |
| No pack is sent | `resource-pack.enabled` is `false` or `url` is empty | Check [the keys](config-core.md#resource-pack). |

## Links

- [DwellerCore resource pack keys](config-core.md#resource-pack)
- [DwellerTools config](config-tools.md)
- [DwellerEnchants config](config-enchants.md)
- [Minecraft resource pack format on the wiki](https://minecraft.wiki/w/Resource_pack)
- [GitHub Actions documentation](https://docs.github.com/en/actions)

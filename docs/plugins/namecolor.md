# NameColor

**Version 1.11.5** (wiicart). Lets players color and style their display name, set a nickname with colors, apply gradients, and look up who a nickname belongs to. Softdepends on Essentials, ProtocolLib and PlaceholderAPI. Player page: [Name colors](https://craftyplayz.github.io/DwellerMC-wiki/plugins/namecolor/).

## Overlap with Essentials
Both plugins register these commands:

| Command | NameColor | EssentialsX 2.22.0 |
|---|---|---|
| `/nick`, `/nickname` | Set nickname (colors if `namecolor.nick.color`) | Set nickname (aliases `enick`, `nickname`, `enickname`) |
| `/whois` | Find a player by nickname | Basic information about a player (alias `ewhois`) |

Because NameColor softdepends Essentials, Essentials loads first and normally owns the plain `/nick`, `/nickname` and `/whois`. NameColor's versions are then available as `/namecolor:nick`, `/namecolor:nickname`, `/namecolor:whois`. Run `/nick` and `/help nick` once on the server to see which plugin answers.

Things that do not overlap: `/namecolor`, `/color`, `/gradient` exist only in NameColor.

How they share the work:
- `mode: auto` in NameColor's config picks "essentials" mode when Essentials is present, so nicknames are stored as Essentials nicknames (and fall under Essentials' `nickname-prefix: '~'`, `max-nick-length: 16` and `allowed-nicks-regex: '^[a-zA-Z_0-9§]+$'`). Set `mode: essentials` to make that explicit.
- Essentials' `change-displayname: true` means the display name follows the nickname.
- Chat: EssentialsChat's format is `<{DISPLAYNAME}> {MESSAGE}`, so a colored display name or nickname shows in chat automatically. EssentialsChat does not color the *message*; that needs `essentials.chat.color` and friends.
- Essentials' own nick permissions (`essentials.nick`, `essentials.nick.color`, `essentials.nick.rgb`, `essentials.nick.magic`) default to op. NameColor's nick permissions default to **true**. Because of the command overlap, the plain `/nick` command asks for Essentials' permissions. Decide which plugin owns nicknames:

**Recommended:** let **Essentials own `/nick`** (it is already installed and has a blacklist and `/realname`), and use NameColor only for `/namecolor` and `/gradient`. Then either deny or ignore NameColor's nick nodes, and grant `essentials.nick` to players if you want nicknames at all. Alternatively, set `enable-nick: false` in NameColor (`/nick` replies "Nicknames are currently disabled" when NameColor handles it).

## Commands
| Command | Permission | Effect |
|---|---|---|
| `/namecolor <color> [styles...] [player]` | `namecolor.set` (+ `.others` for others) | Set your name color. Colors by name or `#RRGGBB`. Styles: `bold`, `underline`, `italics`, `magic`, `strike`. |
| `/color` | none | Show the color list (turn off with `color-command: false`). |
| `/gradient <color1> <color2> [player]` | `namecolor.gradient` (+ `.others`) | Two-color gradient on the display name. Preset gradients live in `gradients.yml`. |
| `/nick`, `/nickname <nick> [player]` | `namecolor.nick` (+ `.others`, `.color`) | See the overlap section. |
| `/whois <display name>` | `namecolor.whois` | See the overlap section. |

## Permissions
| Node | Default | Effect |
|---|---|---|
| `namecolor.*` | op | Set, set.others, nick, nick.others, whois, styles, colors. |
| `namecolor.set` | true | Includes `set.style` and `set.color`. |
| `namecolor.set.color` | true | All colors and hex. Specific: `namecolor.set.color.<black, darkblue, darkgreen, darkaqua, darkred, darkpurple, gold, gray, darkgray, blue, green, aqua, red, pink, yellow, white, hex>`. |
| `namecolor.set.style` | true | All styles. Specific: `namecolor.set.style.bold`, `.italic`, `.underline`, `.magic`, `.strike`. |
| `namecolor.set.others` | op | Color others. |
| `namecolor.nick` | true | `/nick` for yourself. |
| `namecolor.nick.others` | op | Nick others. |
| `namecolor.nick.color` | true | Colors inside nicknames. |
| `namecolor.whois` | true | `/whois`. |
| `namecolor.gradient` | true | `/gradient`. |
| `namecolor.gradient.others` | op | Gradient on others. |
| `namecolor.filter.bypass` | op | Ignore nickname blacklist. |

The specific color and style nodes have no default of their own but are children of `namecolor.set.color` / `namecolor.set.style`, so everyone gets them.

### Recommended default group
```
/lp group default permission set namecolor.set.style.magic false
/lp group default permission set namecolor.nick false
/lp group default permission set namecolor.whois false
```
The first stops the unreadable "magic" (obfuscated) style, which is easy to abuse on a small server. The other two keep NameColor's nickname and whois out of the way of Essentials. Keep `namecolor.set`, `namecolor.gradient` default. Staff get `namecolor.set.others`.

## Config (`plugins/NameColor/config.yml`)
| Key | Default | Effect |
|---|---|---|
| `prefix` | `&8[&dNameColor&8] &f` | Message prefix. |
| `mode` | `auto` | `auto`, `server`, `essentials`. How nicknames are applied. |
| `storage` | `yml` | `yml` (`players.yml`), `mysql`, `mariadb`. |
| `database.host/port/database/user/password` | `localhost / 3306 / db / username / password` | For mysql or mariadb. |
| `max-nickname-length` | `16` | Without color codes. |
| `allow-username-nicknames` | `true` | Allow another player's real name as a nickname. Set `false` to prevent impersonation. |
| `notify-players` | `true` | Tell players when their display name changes. |
| `nick-prefix` | `''` | Text before nicknames (Essentials uses `~`). |
| `color-command` | `true` | Enable `/color`. |
| `enable-nick` | `true` | Enable NameColor's `/nick`. |
| Messages | see file | `namecolor-help`, `nickname-help`, `name-set` (`%display-name%`), `name-set-other` (`%username%`, `%display-name%`), `whois-message`, `no-perms`, `invalid-*`, `nick-too-long`, `username-nick-prohibited`, `nick-blacklist`, `no-perms-style`, `style-specific` (`%style%`), `color-specific` (`%color%`), `unknown-style`, `nick-disabled`, and others. `&` codes allowed. |

### Other files
- `blacklist.yml`: `fuzzy: true`, `max-percentage: 0.2`, `normalize: true`, `blacklist: ["gerlnerg"]` and a `regex` list (empty). Add forbidden nicknames here. Fuzzy matching is recommended over regex.
- `gradients.yml`: preset gradients, default `green-purple` (`#8FF184` to `#F025FF`).
- `players.yml`: saved colors per player (YAML storage).

## Interactions
- **TAB (installed)**: TAB can replace names in the tab list and above heads with its own formatting. If colors from NameColor do not appear there, TAB's nametag/tablist settings override them.
- **LuckPerms**: prefixes/suffixes are separate from NameColor; you can combine them in the chat format.
- **DwellerCore**: no overlap (the depth HUD uses the action bar).

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| `/nick` does nothing for players | Essentials owns the command and needs `essentials.nick` | Grant it or switch ownership. |
| Plain `/whois` shows Essentials output | Expected, Essentials registers first | Use `/namecolor:whois` or leave it. |
| Color not showing in chat | A formatter that ignores display names | EssentialsChat's default shows it; check other chat plugins. |

Back to the [admin index](../index.md) or [troubleshooting](../troubleshooting.md).

## Links
- [NameColor website](https://namecolor.pedestria.com)

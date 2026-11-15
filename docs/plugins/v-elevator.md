# V-Elevator

**Version 1.0.5** (author reakkz, Paper 1.21+ API). Players stand on a special block and press **Jump** to go up or **Sneak** to go down to the next elevator block in the same column.

Player page: [V-Elevator](https://craftyplayz.github.io/DwellerMC-wiki/plugins/v-elevator/).

## How it works
- An "elevator block" is a normal block (`WHITE_WOOL` by default) that was **placed from the special elevator item**. Placing it adds its location to `plugins/V-Elevator/elevators.yml` (keys under `elevators:`; the file section is `elevator-data`).
- A block is an elevator **only because its coordinates are in `elevators.yml`**. The block's material is never re-checked afterwards.
- Jump (a move event where the player was on the ground and Y rises) or start sneaking, while standing on a registered elevator, scans the same X/Z column for the nearest registered elevator in that direction, up to `elevator.max-floor-distance` blocks.
  - Up: Y+1 .. Y+distance, stopping below the world max height.
  - Down: Y-1 .. Y-distance, stopping above the world min height.
- The player is teleported 1 block above the target elevator, with their yaw/pitch kept. Sound, particles and action bar/title messages follow.
- There is **no check** for blocks between the two elevators and no check that the space above the target is free. A tunnel is not required; a player can go through solid rock to an elevator 40 blocks away. A target buried in rock puts the player inside blocks (suffocation damage).
- Pistons cannot push or pull elevator blocks (the event is cancelled). Breaking one (not in Creative) drops the elevator item and removes the registration.
- Requires `elevator.use` to ride. Placement is blocked in `blocked-worlds` (the message is the no-permission text with the prefix).

## DwellerMC notes
### Distance in a 2,032-block-tall world
The world runs from Y -2032 up to the roof (about Y 6..15, see [world layout](https://craftyplayz.github.io/DwellerMC-wiki/world/layout/)). The plugin uses the world's own min/max height, so the whole column is reachable. The only limit is `elevator.max-floor-distance`.

- **Default 50**: next elevator must be at most 50 blocks above or below. Elevators placed further apart do not link. Chain several elevators (one every 50 blocks or fewer) for long shafts: each Jump/Sneak is one hop to the *nearest* elevator in that direction.
- The value is just a loop bound, so large values are cheap (one block lookup per step). `2100` would cover the entire world in one hop. Recommended: **`200`** for survival (a 200-block shaft is a real investment) or higher if you want fast travel across the depth bands. Keep it finite: a very large value lets one elevator pair bridge a whole biome through rock.
- Because it is only a scan, a player can reach any elevator in range even if there is rock in between. If you want elevators to need an open shaft, this plugin cannot enforce it.

### Other points
- No surface or sky needed; nothing in the plugin depends on weather or light.
- The **recipe** needs an **ender pearl** (8 white wool around 1 pearl). Ender pearls are rare underground. Players get them from mobs; consider handing out elevators with `/elevator give`, or changing the `crafting.ingredients.E` item.
- **Stale registrations (ghost elevators).** Only block break by a player and pistons are handled. If an elevator block is destroyed by an **explosion** (creeper, TNT), fire, fluids, WorldEdit, or another plugin, its entry stays in `elevators.yml`. Players can then still travel to that empty location and are teleported into the air above it. Fix by editing `elevators.yml` (server stopped) or by placing and breaking a real elevator on that spot. DwellerMC has many mobs and explosive hazards, so expect this.
- The same applies to WorldEdit pastes/undo: copied wool is **not** an elevator; deleted elevators stay registered.
- The registration list is loaded into memory at start. Edit `elevators.yml` only while the server is stopped.
- `/elevator reload` re-reads `config.yml` (messages, sound, particles, distance, blocked worlds). Restart the server after changing `crafting.*` or `elevator-item.*`.
- The item is recognised by **material + custom model data** (`WHITE_WOOL` with a model-data number the plugin sets). Players can still place ordinary white wool; it is simply not an elevator. If you change `elevator-item.material` later, previously issued items no longer match.
- Anyone who can break blocks can break someone's elevator and take the item. Protect bases with WorldGuard regions.

## Commands
| Command | Permission | Effect |
|---|---|---|
| `/elevator reload` | `elevator.admin` | Reload `config.yml`. |
| `/elevator give <player> [amount]` | `elevator.admin` | Give elevator blocks. Tab-complete offers 1, 16, 32, 64. |

There is only one command; there are no player commands. Jump / Sneak is the whole interface.

## Permissions
| Node | Default | Effect |
|---|---|---|
| `elevator.use` | **true** (everyone) | Ride elevators. Checked every time. |
| `elevator.craft` | **true** | Craft the elevator item (checked in the craft listener). |
| `elevator.admin` | op | `/elevator reload` and `give`. |

Recommended LuckPerms: nothing for the `default` group (the defaults already grant use and craft). To make elevators staff-given only: `lp group default permission set elevator.craft false` and also set `crafting.enabled: false`.

## Config (`plugins/V-Elevator/config.yml`)

| Key | Default | Effect |
|---|---|---|
| `elevator-item.material` | `WHITE_WOOL` | Item and block material (valid Bukkit `Material`). |
| `elevator-item.display-name` | `&bElevator Block` | Item name (`&` codes). |
| `elevator-item.lore` | 3 lines | Item lore. |
| `crafting.enabled` | `true` | Register the recipe. |
| `crafting.result-amount` | `1` | Items per craft. |
| `crafting.shape` | `WWW / WEW / WWW` | 3x3 pattern. |
| `crafting.ingredients.W` / `.E` | `WHITE_WOOL` / `ENDER_PEARL` | Ingredients by letter. |
| `elevator.max-floor-distance` | `50` | Max blocks scanned up/down. See above. |
| `elevator.teleport-sound` | `ENTITY_ENDERMAN_TELEPORT` | Bukkit sound name; `''` disables. |
| `elevator.sound-volume` | `0.5` | 0.0-1.0. |
| `elevator.sound-pitch` | `1.0` | Pitch. |
| `particles.enabled` | `true` | Particles at both ends. |
| `particles.type` | `PORTAL` | Bukkit `Particle`. |
| `particles.count` | `20` | Count. |
| `particles.offset-x/y/z` | `0.3 / 0.5 / 0.3` | Spread. |
| `particles.speed` | `0.05` | Speed. |
| `messages.actionbar-enabled` | `true` | Action bar text. |
| `messages.actionbar-up` / `-down` / `-no-floor` | arrows / "No floor found in that direction!" (small caps) | Texts. |
| `messages.title-enabled` | `false` | Title + subtitle. |
| `messages.title-up/-down`, `title-subtitle-up/-down` | defaults | Title texts. |
| `messages.prefix` | `&8[&bElevator&8] &r` | Chat prefix. |
| `messages.reload-success`, `give-success`, `no-permission`, `player-not-found` | defaults | Chat texts (`{amount}`, `{player}`). |
| `blocked-worlds` | `world_example_1`, `world_example_2` | Worlds where elevators do not work or place. **Placeholders: replace or leave** (they match nothing). |
| `debug` | `false` | Log place/break/teleports. |

Messages in the default file use Unicode small-caps letters; they display correctly in the client but are awkward to edit. Replace with plain text if you prefer.

## Data
- `plugins/V-Elevator/elevators.yml`: registered block positions. **Back this up**; losing it turns every elevator into plain wool.
- `plugins/V-Elevator/config.yml`.

## Interactions
- **WorldGuard / CoreProtect**: placement/break are normal Bukkit events; protection plugins that cancel the event stop registration (the plugin checks `isCancelled` on place and break). CoreProtect logs the wool block itself.
- **DwellerCore**: no overlap. Elevators ignore mob levels and depth.

## Problems
| Symptom | Cause | Fix |
|---|---|---|
| "No floor found" | Next elevator is beyond `max-floor-distance`, not in the exact same X/Z, or was never registered | Raise the distance; place with the real item. |
| White wool does nothing | Placed from normal wool | Use the item from `/elevator give` or the recipe. |
| Teleport into empty air / wall | Ghost entry or unobstructed target | See "Stale registrations". |
| Recipe unavailable | Needs `elevator.craft`, or `crafting.enabled: false` | Check permissions. |


# DwellerEnchants config

File: `plugins/DwellerEnchants/config.yml` (default: `DwellerEnchants/src/main/resources/config.yml`). Keys sit under `enchants:`, `books:`, `drops:`, `attune:`, `anvil:`, `grindstone:` and `mending:`.

**When changes apply:** a full **restart**. There is no reload command. The enchantments are registered by a datapack that is built before worlds load, so never use `/reload`.

**Bad values never stop the server.** A wrong type, a number outside the range or an unknown id logs a warning that names the key, and the default is used. A key missing from your file uses the default from the jar.

## What it does

DwellerEnchants adds 27 custom enchantments in 5 rarities (Common, Uncommon, Rare, Legendary, Mythic). They are real registered enchantments, so they get the glint, the vanilla tooltip and vanilla anvil combining. They never show up in enchanting tables, trades or loot. The only source is books:

1. Players kill leveled mobs; the mobs drop **unattuned books** ([`drops`](#drops)).
2. Right-clicking an unattuned book **attunes** it to a random enchantment of the same rarity, a random level and an application chance ([`attune`](#attune)).
3. An anvil applies the attuned book to an item. The application is rolled when the result is taken; on a failure the levels are spent, the book is destroyed and the item is unchanged ([`anvil`](#anvil)).
4. A custom grindstone menu removes one chosen enchantment ([`grindstone`](#grindstone)).
5. There is no Mending ([`mending`](#mending)).

There are no commands and no permissions.

!!! warning "Requires DwellerCore, restart, never remove an enchantment"
    - DwellerEnchants needs [DwellerCore](config-core.md). It reads DwellerCore's mob level and elite data keys (`dwellercore:level`, `dwellercore:hidden_level`, `dwellercore:elite_traits`) and its `mobs.curve-exponent` and `mobs.max-level`. Without leveled mobs, books never drop.
    - It loads at `STARTUP` (after DwellerCore) and registers its datapack in the bootstrapper. Config changes need a full restart, never `/reload`.
    - **Never remove an enchantment from the config.** Items carry the enchantment id, and an id that is not registered breaks them. Turn one off with `enabled: false` instead: it stops dropping, attuning and applying and its effect is off, but it stays registered so items that have it keep showing it.

## Install

1. Build: `./gradlew build` in `DwellerEnchants/`. The jar is `build/libs/DwellerEnchants-<version>.jar`.
2. Copy it into `plugins/` next to DwellerCore. Needs Paper 26.3 and Java 25.
3. Restart. The first start copies `config.yml` into `plugins/DwellerEnchants/` and writes the datapack to `plugins/DwellerEnchants/datapack/`. The folder is rebuilt from the config at every startup, so do not edit it by hand. `/datapack list enabled` shows `dwellerenchants`.
4. Players need the [resource pack](resource-pack.md) for the silent attune animation (see [attune](#attune)). Everything else works without it.

The ids are `dwellerenchants:<name>` (lower case, for example `dwellerenchants:elite_bane`) and the config keys are `enchants.<name>`.

## Shared rules

- **Bonuses add, then multiply.** Percent damage bonuses (Elite Bane, Giant Killer, Depth Slayer, Meteor) are added together and the hit is multiplied once: +45% and +30% = +75%, so x1.75.
- **Reductions multiply.** Damage reductions (Elite Ward, Bulwark, Last Stand) multiply each other: -60% and -48% = 0.4 x 0.52, so -79%. Pieces of the same enchantment first add up (four pieces of Bulwark IV = -48%).
- **Full-charge hit** means the attack bar was at least 90% recharged, vanilla's own rule for sweeps and critical hits. Disarm, Shatter, Silence, Cleave and Executioner need one. Frostbite and Lifesteal work on every hit.
- **PvP.** Everything works against players too, except where a row says otherwise (Disarm and Silence are for mobs only; Elite Bane, Elite Ward, Depth Slayer and Bulwark only matter against elites or leveled mobs).
- **Leveled mob and elite** are DwellerCore terms: a leveled mob carries a DwellerCore level (natural spawns in the cave world and the surface; not spawner mobs, bosses or players), and an elite is a leveled mob with elite traits.
- **Per-level lists.** A value written as a list (`[0.15, 0.3, 0.45]`) is level I, II, III. Lists are cut at `max-level`, and a shorter list repeats its last value for the higher levels.
- **`max-level`.** 1 to 10 per enchantment. Lowering it leaves higher levels on existing items in place; only new books are limited.
- **`incompatible-with`.** Ids of enchantments that cannot share an item with this one. An id without a namespace is `minecraft:`. It is written into the datapack, so it needs a restart.
- **Seconds** are real seconds, chances and fractions are 0 to 1 (0.15 = 15%).
- **Datapack-driven enchantments** are Frostbite, Grounded, Vitality and Unbreakable. Their effect is in the generated datapack, not in plugin code. Their numbers only change on restart, like every name, max level and incompatibility.
- **Damage types.** The plugin adds two: `dwellerenchants:true_damage` (Executioner's extra damage; ignores armor, Protection and the hit cooldown) and `dwellerenchants:splash` (Cleave and Quake splash; normal damage). Neither causes knockback. Plugins that look at damage types see them under these ids.

## Keys every enchantment has

These sit under `enchants.<id>.` for all 27.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `enabled` | `true` | boolean | `false`: never dropped, never attuned, never applied by an anvil, effect off. Still registered. |
| `name` | see tables | text | Shown on items and books. Written into the datapack. |
| `description` | see tables | text | One line in the lore of attuned books (`<description>` in `books.attuned-lore`). |
| `max-level` | see tables | 1 to 10 | Highest level. Per-level lists are cut here. |
| `incompatible-with` | see tables | enchantment ids | Only on enchantments that have a default. See [shared rules](#shared-rules). |

## Common

| Id | Goes on | Key | Default | Range | Effect |
|---|---|---|---|---|---|
| `frostbite` (max 2) | swords, axes, maces | `slowness-level` | `[1, 2]` | 1 to 10 | Slowness level per enchantment level (Slowness I = 15% slower, II = 30%). |
| | | `seconds` | `[3, 5]` | 0 to 600 | Slowness duration. Every direct hit slows, weak hits and mace smashes included, on any target (mobs, elites, bosses, players). Datapack-driven. |
| `bloodlust` (max 3) | melee | `regeneration-level` | `[1, 2, 2]` | 1 to 10 | Regeneration level per enchantment level. |
| | | `seconds` | `[2, 2, 5]` | 0 to 600 | Duration. A kill with the weapon (the final hit, or Cleave and Quake splash) gives it; a new kill restarts the timer. Any kill counts, animals and players too. |
| `grounded` (max 3) | boots | `knockback-resistance` | `[0.1, 0.2, 0.3]` | 0 to 1 | Knockback resistance added while worn (1 = no knockback at all; a full netherite set already gives 0.4). Does not touch explosion knockback. Datapack-driven. |
| `night_sight` (max 1) | helmets | none | | | Night Vision while worn, in every world. The effect icon shows, its particles don't. Potion Night Vision is left alone and Darkness is not affected. |
| `escape_artist` (max 3) | boots | `health-fraction` | `0.3` | 0 to 1 | Triggers when any damage leaves your health below this fraction of your max. |
| | | `speed-level` | `[2, 3, 3]` | 1 to 10 | Speed level per enchantment level. |
| | | `speed-seconds` | `[3, 4, 4]` | 0 to 600 | Speed duration. |
| | | `resistance-level` | `[0, 0, 1]` | 0 to 10 | Resistance level, `0` = none (only level III gives it). |
| | | `resistance-seconds` | `[0, 0, 4]` | 0 to 600 | Resistance duration. |
| | | `cooldown-seconds` | `[30, 20, 20]` | 0 to 3600 | Cooldown after a trigger. Nothing shows it. |

"Melee" means swords, axes and maces. Names: Frostbite, Bloodlust, Grounded, Night Sight, Escape Artist.

## Uncommon

| Id | Goes on | Key | Default | Range | Effect |
|---|---|---|---|---|---|
| `disarm` (max 2) | melee | `chance` | `[0.05, 0.1]` | 0 to 1 | Chance per full-charge hit on a mob to knock its weapon (bows and crossbows too) out of its hands. |
| | | `seconds` | `10` | 1 to 600 | How long the mob stays disarmed. The weapon lies on the ground as a visual nobody can pick up and returns when the timer ends; a new proc restarts the timer; if the mob dies first the weapon is gone (mob gear never drops). Does nothing to players. |
| `elite_bane` (max 3) | melee | `damage-bonus` | `[0.15, 0.3, 0.45]` | 0 to 100 | Extra damage against elites, added to the other percent bonuses (0.15 = +15%). A weapon with Elite Bane also goes through the Shielded trait (see [DwellerCore and DwellerSurface](#dwellercore-and-dwellersurface)). |
| `vitality` (max 3) | any armor piece | `health` | `[2, 4, 6]` | 0 to 1000 | Max health added per piece per level (2 = one heart). Stacks across all four pieces (up to +24 at level III). Datapack-driven. |
| `elite_ward` (max 3) | any armor piece | `reduction` | `[0.05, 0.1, 0.15]` | 0 to 1 | Less damage from an elite's own hits and projectiles (not its death blast or its minions), per piece, stacking across pieces (four pieces at III = -60%). Multiplies with the other reductions. |
| `purity` (max 3) | helmets | `reduction` | `[0.33, 0.55, 0.75]` | 0 to 1 | Fraction of the duration removed from every harmful effect you receive (mob hits, arrows, witch potions, elite auras, wardens, players). 0.75 = effects last a quarter as long. Beneficial effects are untouched. |

Names: Disarm, Elite Bane, Vitality, Elite Ward, Purity.

## Rare

| Id | Goes on | Key | Default | Range | Effect |
|---|---|---|---|---|---|
| `sunder` (max 5) | melee | `ignored` | `[0.15, 0.3, 0.45, 0.6, 0.75]` | 0 to 1 | Fraction of the target's armor, toughness and Protection reduction that your hits ignore. Stacks with Shatter. `incompatible-with: [breach]` (Breach does the same for maces). |
| `cleave` (max 3) | melee | `fraction` | `[0.3, 0.45, 0.6]` | 0 to 10 | A full-charge hit also damages every other mob (never players) around the target for this fraction of the hit's damage, through each mob's own armor. |
| | | `radius` | `3` | 0 to 32 | Radius around the target, in blocks. Splash kills count for Bloodlust and Lifesteal; on-hit effects (Frostbite, Disarm...) don't trigger on splash. `incompatible-with: [sweeping_edge, "dwellerenchants:quake"]`. |
| `giant_killer` (max 3) | melee | `damage-bonus` | `[0.1, 0.2, 0.3]` | 0 to 100 | Full bonus against a target with at least `full-bonus-at` times your max health, added to the other percent bonuses (0.1 = +10%). |
| | | `full-bonus-at` | `2.0` | 1 to 100 | Target max health, as a multiple of yours, at which the full bonus applies. The bonus grows from 0 at equal health. Your real max health counts, Vitality included. |
| `deflection` (max 3) | chestplates | `chance` | `[0.1, 0.2, 0.3]` | 0 to 1 | Chance per projectile (arrows, tridents, fireballs, shulker bullets, wither skulls, wind charges...) to do nothing to you. Arrows drop to the ground; fireballs and skulls vanish. |
| `dodge` (max 3) | leggings | `chance` | `[0.04, 0.08, 0.12]` | 0 to 1 | Chance to take no damage from a melee hit, projectile or explosion: no knockback, no status effect from that hit, no sound or particles. Falls, fire, lava and poison can't be dodged. |
| `quake` (max 3) | maces | `fraction` | `[0.3, 0.45, 0.6]` | 0 to 10 | Like Cleave, but on a mace smash attack: a fraction of the full smash damage (fall bonus, Density and Meteor included) to every other mob around the target. |
| | | `radius` | `3` | 0 to 32 | Radius in blocks. Same rules as Cleave. `incompatible-with: ["dwellerenchants:cleave"]`. |

Names: Sunder, Cleave, Giant Killer, Deflection, Dodge, Quake.

## Legendary

| Id | Goes on | Key | Default | Range | Effect |
|---|---|---|---|---|---|
| `shatter` (max 3) | melee | `per-stack` | `0.05` | 0 to 1 | Each full-charge hit adds a stack that removes this fraction of the target's armor and toughness (0.05 = 5% of what it has). |
| | | `max-stacks` | `[3, 6, 9]` | 1 to 100 | Most stacks at once, per level. |
| | | `seconds` | `10` | 1 to 600 | Each stack lasts its own time. Stacks sit on the target, so everyone hitting it benefits. Stacks with Sunder. |
| `silence` (max 1) | melee | `chance` | `0.15` | 0 to 1 | Chance per full-charge hit to silence a DwellerCore mob. |
| | | `seconds` | `5` | 1 to 600 | Duration; a new proc restarts it. Nothing on players. See [DwellerCore and DwellerSurface](#dwellercore-and-dwellersurface). |
| `last_stand` (max 2) | chestplates | `health-fraction` | `0.3` | 0 to 1 | Active while your health, before the hit, is below this fraction of your max. |
| | | `reduction` | `[0.15, 0.3]` | 0 to 1 | Less damage of every kind (falls, lava and poison too). Multiplies with the other reductions. Can share a chestplate with Second Wind. |
| `second_wind` (max 1) | chestplates | `health-fraction` | `0.25` | 0 to 1 | Triggers when any damage leaves you below this fraction of your max health. |
| | | `regeneration-level` | `3` | 1 to 10 | Regeneration level given. |
| | | `seconds` | `5` | 0 to 600 | Regeneration duration. |
| | | `cooldown-seconds` | `60` | 0 to 3600 | Cooldown. Nothing shows it. |
| `overheal` (max 2) | leggings | `max-absorption` | `[4, 8]` | 0 to 100 | Most absorption from Overheal at once (4 = two yellow hearts). Healing past full health (potions, Regeneration, Bloodlust, Second Wind, Lifesteal; not natural regeneration from food) becomes absorption. |
| | | `fade-seconds` | `10` | 0 to 3600 | Seconds without dealing or taking damage before it disappears. Golden apple absorption is separate and untouched. |
| `unbreakable` (max 1) | anything with durability | none | | | The item never loses durability. Applying it fully repairs the item and removes Unbreaking from it; Unbreaking can't be added afterwards (`incompatible-with: [unbreaking]`). Replaces Mending. Can't go on surface suit pieces. Datapack-driven. |
| `meteor` (max 2) | maces | `per-block` | `[0.15, 0.3]` | 0 to 100 | Damage bonus per block fallen (0.15 = +15% per block), added to the other percent bonuses and applied to the whole smash. |
| | | `max-blocks` | `10` | 0 to 1000 | Blocks counted at most (10 blocks x 0.3 = +300% at level II). `incompatible-with: [density]`. |

Names: Shatter, Silence, Last Stand, Second Wind, Overheal, Unbreakable, Meteor.

## Mythic

| Id | Goes on | Key | Default | Range | Effect |
|---|---|---|---|---|---|
| `executioner` (max 3) | melee | `fraction` | `[0.02, 0.04, 0.06]` | 0 to 1 | Each full-charge hit also deals this fraction of the target's max health as extra damage (type `dwellerenchants:true_damage`, so it ignores armor, Protection and the defensive enchantments). Percent damage bonuses don't raise it. A weapon with Executioner also goes through the Shielded trait. |
| | | `bosses` | `[warden, wither, ender_dragon, elder_guardian]` | mob ids as in `/summon` | Mobs in this list take at most `boss-cap` extra damage per hit. |
| | | `boss-cap` | `10` | 0 to 1000 | The cap, in health points. |
| `depth_slayer` (max 5) | melee | `bonus-at-max` | `[0.5, 1.0, 1.5, 2.0, 2.5]` | 0 to 100 | Damage bonus against a level 50 mob, per enchantment level (2.5 = +250%), added to the other bonuses. Only leveled mobs. It follows DwellerCore's power curve: nothing at level 1, about 40% of the bonus at level 25, the full bonus at level 50 and still more above it. |
| `lifesteal` (max 3) | melee | `fraction` | `[0.03, 0.06, 0.09]` | 0 to 10 | Heals you for this fraction of all damage you deal with the weapon: the hit, Executioner's extra damage and Cleave or Quake splash, on any hit, with no cap. Overflow feeds Overheal. |
| `bulwark` (max 4) | any armor piece | `reduction` | `[0.03, 0.06, 0.09, 0.12]` | 0 to 1 | Less damage from leveled mobs of any level (their hits, projectiles and explosions), per piece. The pieces add up (four pieces at IV = -48%), and that total multiplies with the other reductions. |

Names: Executioner, Depth Slayer, Lifesteal, Bulwark.

!!! note "Depth Slayer and the power curve"
    Depth Slayer uses DwellerCore's `mobs.curve-exponent` and `mobs.max-level`, read once at startup. The 40% at level 25 above holds for the default exponent 1.32. If you change either in DwellerCore, restart the server. The hidden level scale (1 to 5000) is fixed in DwellerCore.

## DwellerCore and DwellerSurface

- **Silence** turns off for its duration, while DwellerCore's [`mobs.silence-respected`](config-core.md#levels-and-stats) is `true`: every elite trait (active, passive and Explosive death), status hits, archer aim and double shots, strong witch potions, extra slime splits, spider leaps, enderman teleports and the zombie reinforcement bonus. Stats, gear, name tag, elite particles and boss bar stay. It is stored on the mob as `dwellerenchants:silenced_until` ([keys](#persistent-keys)).
- **Shielded.** An elite's Shielded trait blocks its first hits completely. A player whose main-hand weapon has an enchantment listed in DwellerCore's [`elites.traits.shielded.pierced-by`](config-core.md#traits) (default Elite Bane and Executioner) hits through, also with arrows and tridents they shot, and uses no charge. The list is DwellerCore's setting, not this plugin's.
- **Thorns.** DwellerCore's Thorny trait reflects the damage from before DwellerEnchants' bonuses (DwellerCore handles the hit at `HIGH`, this plugin adds its bonuses at `HIGHEST`). A high Elite Bane does not make thorny elites hurt more.
- **Elite Ward and Bulwark** read the same level and elite keys, so they only apply against mobs DwellerCore leveled.
- **Drops.** The mob's DwellerCore level stands in for depth, so the surface's fixed level 50 drops like the bottom of the cave world. DwellerCore's own `enchanted_book` rare drop never rolls these enchantments or Mending ([`mobs.rewards.book-excluded-enchantments`](config-core.md#rewards)).
- **DwellerSurface.** Unbreakable can't go on surface suit pieces: [`suit.blocked-enchantments`](config-surface.md) lists `dwellerenchants:unbreakable` by default. This plugin's anvil handler runs first, so DwellerSurface can still clear the result for a suit piece.

## books

Settings for the five rarities and the item text. Every enchantment belongs to one rarity (fixed, see the tables above).

| Key | Default | Unit | Effect |
|---|---|---|---|
| `books.rarities.<rarity>.name` | Common, Uncommon, Rare, Legendary, Mythic | text | Rarity name, `<rarity>` in the text keys. |
| `books.rarities.<rarity>.color` | white, green, blue, gold, light_purple | color name or `#rrggbb` | Rarity color: book names and `<color>` in the text keys. |
| `books.rarities.<rarity>.chance` | common `[0.5, 0.6]`, uncommon `[0.6, 0.7]`, rare `[0.7, 0.8]`, legendary `[0.8, 0.95]`, mythic `[0.95, 1.0]` | two values, 0 to 1 | Application chance range. An attuned book rolls one value between these when it is attuned and keeps it. |
| `books.rarities.<rarity>.anvil-cost` | common 1, uncommon 2, rare 4, legendary 8, mythic 8 | levels per enchantment level, 0 to 40 | Anvil cost, like vanilla's 1 (common) to 8 (very rare). Written into the datapack. |
| `books.unattuned-name` | `<color>Unattuned <rarity> Book` | MiniMessage | Name of unattuned books. |
| `books.unattuned-lore` | two grey lines | list of MiniMessage | Lore of unattuned books. |
| `books.attuned-lore` | description, max level, application chance, rarity | list of MiniMessage | Lore of attuned books, under the enchantment line vanilla adds. Placeholders `<description>`, `<max_level>`, `<chance>` (whole percent), `<rarity>`, `<color>`. The book itself is named after the enchantment and level (Sunder II) in the rarity color. |

Unattuned books stack to 64 like plain books.

## drops

| Key | Default | Unit | Effect |
|---|---|---|---|
| `drops.chance` | `[0.01, 0.06]` | two values, 0 to 1 | Chance that a kill by a player drops an unattuned book, at mob level 1 and at DwellerCore's max mob level, linear between. The mob's level stands in for depth. Spawner mobs have no level and drop nothing; trial spawner mobs are leveled and drop normally. |
| `drops.elite-multiplier` | `5` | 0 to 100 | Elites multiply the chance by this. |
| `drops.looting-per-level` | `0.01` | 0 to 1 | Added to the chance per level of Looting on the killer's weapon (0.01 = +1 percentage point). |
| `drops.hostile-only` | `true` | boolean | `true`: only hostile mobs drop books. `false`: every leveled mob. |
| `drops.weights-top` | `{ common: 70, uncommon: 22, rare: 7, legendary: 1, mythic: 0 }` | relative weights | Rarity weights at mob level 1. |
| `drops.weights-bottom` | `{ common: 22, uncommon: 28, rare: 25, legendary: 15, mythic: 10 }` | relative weights | Rarity weights at the max mob level. The two sets are blended linearly by level, and the chance of a rarity is its weight divided by the total. |

At the defaults a level 1 mob drops a book 1% of the time and a level 50 mob 6% of the time (30% for an elite, before Looting). Mythic books only appear well down the world.

!!! warning "Drop rate"
    `drops.chance` and `drops.elite-multiplier` multiply. A chance of `[1, 1]` is every kill: use it for testing and nothing else.

## attune

Right-clicking an unattuned book (anywhere, one book at a time) turns it into an attuned book of the same rarity: a random enabled enchantment of that rarity, a random level from I to its max, and an application chance rolled in the rarity's range.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `attune.animation` | `true` | boolean | `true`: the totem animation plays with the book. Its sound is silenced by the server [resource pack](resource-pack.md#repo-layout) (`assets/minecraft/sounds.json`), not by this key. `false` = no animation. |
| `attune.message` | `<color>Your book became <enchant>` | MiniMessage | Action bar message. `<enchant>` = the enchantment with its level (Sunder II), `<rarity>`, `<color>`. `""` = none. |

## anvil

Applies to every anvil on the server, vanilla enchantments included.

| Key | Default | Unit | Effect |
|---|---|---|---|
| `anvil.max-cost` | `45` | levels, 1 to 1000 | Cost cap. The anvil never says "Too Expensive": once the cost reaches the cap it stays there forever. Vanilla refuses at 40. |
| `anvil.fail-sound` | `minecraft:entity.item.break` | sound id as in `/playsound`; `""` = none | Sound on a failed application. An unknown sound logs a warning and plays nothing. |
| `anvil.fail-message` | `<red>The <enchant> book shattered.` | MiniMessage | Sent on a failure. `<enchant>` = the enchantment with its level, `<chance>` = whole percent. `""` = none. |
| `anvil.success-message` | `""` | MiniMessage | Same placeholders, sent on success. Empty by default. |

How it works:

- **Attuned book on an item.** The application chance is rolled when the result is taken. On a failure the levels are spent, the book is destroyed and the item is unchanged. Creative players pay nothing.
- **Two books of the same enchantment** always combine (I + I = II) and keep the higher chance. Books of different custom enchantments don't combine.
- **Vanilla books** apply with no roll. There is no cap on enchantments per item; custom and vanilla mix freely, minus the incompatibilities.
- **Unbreakable** on an item with Unbreaking replaces it and fully repairs the item.
- **Mending books** are refused while [`mending.remove`](#mending) is `true`.

## grindstone

| Key | Default | Unit | Effect |
|---|---|---|---|
| `grindstone.enabled` | `true` | boolean | `true`: right-clicking a grindstone while holding an enchanted item opens a menu that removes one chosen enchantment, for free, with no XP back and no book. This replaces the vanilla grindstone. `false` = vanilla grindstone. |
| `grindstone.title` | `<dark_gray>Remove an enchantment` | MiniMessage | Menu title. |
| `grindstone.remove-curses` | `false` | boolean | `true`: curses (Binding, Vanishing) can be removed too. Vanilla never allows it. |
| `grindstone.message` | `<gray>Removed <white><enchant></white>.` | MiniMessage | Sent after removing. `<enchant>` = the removed enchantment with its level. `""` = none. |

## mending

| Key | Default | Unit | Effect |
|---|---|---|---|
| `mending.remove` | `true` | boolean | `true`: Mending is taken out of villager trades, chest loot and fishing, and anvils refuse Mending books. Enchanting tables and mob gear never offer it. Items that already have Mending keep it. Needs a restart, because it is done in the datapack. |

The removal is two datapack tags, `minecraft:tradeable` and `minecraft:on_random_loot`, which the datapack replaces with vanilla's lists minus Mending. `#minecraft:on_random_loot` is also the pool of DwellerCore's `enchanted_book` rare drop. The datapack loads on top of the others.

## Persistent keys

Namespace `dwellerenchants`. Never rename one: books and mobs already carry them.

| Key | Type | On | Meaning |
|---|---|---|---|
| `dwellerenchants:book_rarity` | STRING | books | The rarity (`COMMON`, `UNCOMMON`, `RARE`, `LEGENDARY`, `MYTHIC`). Present = the book is one of ours. |
| `dwellerenchants:book_chance` | DOUBLE | attuned books | Application chance, 0 to 1, rolled when the book was attuned. |
| `dwellerenchants:silenced_until` | LONG | mobs | Epoch milliseconds until which the mob is silenced. DwellerCore reads it by name. |

## Testing

- Put an enchantment on an item: `/give @s minecraft:diamond_sword[minecraft:enchantments={"dwellerenchants:sunder":5}]`.
- There is no command that gives a book, and `/give @s minecraft:book` is a plain book. Books only come from mob drops. To test, set `drops.chance` to `[1, 1]`, restart, and kill a leveled hostile mob in the cave world (not a spawner mob). Put the value back afterwards.
- The datapack files are under `plugins/DwellerEnchants/datapack/data/dwellerenchants/enchantment/`, one JSON per enchantment, which shows what the config produced.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Server stops at startup with `could not register datapack` | The datapack folder could not be written | Fix the permissions on `plugins/DwellerEnchants/`. Without the datapack no enchantment exists, so the plugin refuses to start. |
| A change to Frostbite, Grounded, Vitality, a name, a max level or an incompatibility does nothing | It is built into the datapack, and the server was not restarted | Full restart. |
| No books drop | The mob has no DwellerCore level (spawner mob, boss, other world), `drops.hostile-only` excludes it, or DwellerCore is missing | Check [`mobs.level-spawner-mobs`](config-core.md#levels-and-stats) and that DwellerCore is installed. |
| Unbreakable fails on a suit piece | Intended | See [`suit.blocked-enchantments`](config-surface.md). |
| Log: unknown sound for `anvil.fail-sound` | Not a sound id | Use an id from `/playsound` completion, or `""`. |
| Totem sound plays with the attune animation | The resource pack is not applied or is outdated | See [resource pack troubleshooting](resource-pack.md#troubleshooting). |

The [DwellerAdmin](config-admin.md#items) panel lists every attuned book at every level (rolled at the top of its rarity's chance range) and one unattuned book per rarity, through `adminItems`. Disabled enchantments are left out.

## Links

- [DwellerAdmin panel](config-admin.md)
- [Paper documentation](https://docs.papermc.io/)
- [Enchantment definition on the Minecraft Wiki](https://minecraft.wiki/w/Enchantment_definition)
- [MiniMessage format](https://docs.advntr.dev/minimessage/format.html)
- [DwellerCore config](config-core.md)
- [DwellerSurface config](config-surface.md)
- [Resource pack](resource-pack.md)

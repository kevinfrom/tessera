# Assets

Every asset in this repository — sprites, tiles, icons, fonts, sounds — must be listed
here with its source, author and licence. No exceptions, and no asset lands in a commit
before its row does.

This exists so the project can be forked, used and contributed to without anyone having
to guess where a file came from.

## Rules

- **Prefer CC0.** Public domain assets carry no attribution requirement and no downstream
  obligations. This is the default choice.
- **No copyleft.** CC-BY-SA and GPL-licensed art impose obligations that complicate an
  MIT-licensed repository. Don't use it here.
- **CC-BY is acceptable** where the asset is worth the attribution requirement. Credit
  goes in the table below and stays there.
- **Nothing from Jagex.** No sprites, models, map data, sound, cache extracts, or files
  derived from any of the above. Tessera is an original implementation, not a private
  server, and the asset boundary is the clearest line between the two.
- **Nothing scraped from another game.** Same reasoning, any game.
- **Tile size is fixed.** Assets that don't match the project's tile grid are rejected
  rather than rescaled.

## Naming and content

Related to the above, and worth stating plainly because it is easy to do by accident:

- Item, monster, location and NPC names must be **generic fantasy or original**.
  "Bronze sword", "goblin" and "iron ore" are common to the genre and fine. Names
  distinctive to another game's world are not.
- Don't reproduce another game's map layout, quest text, or dialogue.
- Mechanics are fair game. Tick-based combat, stackable inventories and levelling curves
  are design patterns, not property.

## AI-generated assets

Permitted, with two caveats recorded per asset:

- Note the tool used in the table.
- In the US, purely AI-generated images generally cannot be copyrighted. That means we
  can use them freely but cannot claim ownership of them. Fine for this project;
  worth knowing.

For consistency, generated sprites must match the Kenney pack's palette and tile size.
Anything that doesn't sit next to the existing art without looking out of place gets
rejected, regardless of how it was made.

## Primary source

Tessera's art comes from the **[Kenney Roguelike/RPG pack](https://kenney.nl/assets/roguelike-rpg-pack)**,
released under **CC0**. It covers terrain, characters, monsters and items in one coherent
style, which is exactly what a tile-grid game needs.

This is the project's primary source. New art should come from this pack where possible,
and anything added from elsewhere must match its tile size and palette. Mixing packs from
different artists is the fastest way to make a game look like an asset flip.

- **Tile size:** 16 x 16 px, 1 px spacing, no outer margin. See the tile grid section
  below. This is binding for all new art.
- **Palette:** the pack's native palette. New or generated sprites match it rather than an
  external palette.

## Where to find other assets

| Source | Licence | Notes |
|---|---|---|
| [kenney.nl](https://kenney.nl) | CC0 | First stop. Large, coherent, no attribution required. |
| [game-icons.net](https://game-icons.net) | CC-BY 3.0 | Good for inventory and UI icons. |
| [OpenGameArt](https://opengameart.org) | Mixed | Check every asset individually; much of it is copyleft. |
| [itch.io](https://itch.io/game-assets/free) | Mixed | Licence set per pack by the author. |
| [Lospec](https://lospec.com) | — | Palettes, not sprites. |

Pick one primary source and stay with it. Mixing packs from different artists is the
fastest way to make a game look like an asset flip.

## Attribution

| File / directory | Description | Source | Author | Licence |
|---|---|---|---|---|
| `data/assets/kenney-roguelike-rpg-pack/` | Terrain, characters, monsters, items | [kenney.nl](https://kenney.nl/assets/roguelike-rpg-pack) | Kenney | CC0 |

CC0 requires no attribution. The row is here anyway so provenance stays traceable.

## Tile grid

| | Value |
|---|---|
| Tile size | 16 x 16 px |
| Spacing | 1 px |
| Margin | 0 px |

These are the values to enter when importing the tileset into Tiled.

Note the terminology mismatch: the pack's `spritesheetInfo.txt` calls the 1px gap a
"margin", meaning the gap *between* tiles. Tiled calls that **spacing**, and reserves
**margin** for the border around the outside edge of the sheet — which this pack does not
have. Entering 1 in Tiled's margin field will misalign the whole grid.

Getting these wrong shows up as thin seams or bleeding edges between tiles.
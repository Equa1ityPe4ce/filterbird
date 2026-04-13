# FilterBird (PD2 Fork)

A fork of the original [FilterBird](https://github.com/BetweenWalls/filterbird) project by BetweenWalls, adapted for [Project Diablo 2](https://www.projectdiablo2.com/) (PD2). This fork provides a browser-based item filter simulator and is integrated into the PD2 launcher for convenient filter previewing.

**Disclaimer**: This fork may not reflect the exact behavior of item filters in-game. Some displays or item stats may be out of sync with the latest in-game updates.

## Web Usage

FilterBird can be used directly in a browser via the `fromweb` URL parameter.

To open with an empty editor (paste your filter manually):
```
https://equa1itype4ce.github.io/filterbird/index.html?v=PD2&fromweb=true
```

To automatically load a filter from a remote URL, provide the URL-encoded address as the `fromweb` value:
```
https://equa1itype4ce.github.io/filterbird/index.html?v=PD2&fromweb=https%3A%2F%2Fraw.githubusercontent.com%2FMaaaaaarrk%2FHiimFilter-PD2-Filter%2Frefs%2Fheads%2Fmain%2FHiim.filter
```
When a URL is provided, FilterBird will fetch the filter text from that URL and load it automatically. If the fetch fails, it falls back to the empty editor. Both modes set the page background to black and show the filter input.

### Additional URL Parameters

| Parameter   | Values       | Description                                           |
|-------------|--------------|-------------------------------------------------------|
| `fromweb`   | `true` / URL | Opens web mode with an empty editor or loads a filter  |
| `alternate` | `0`          | Disables alternate (custom) formatting                 |
| `auto`      | `0`          | Disables auto-simulation on filter changes             |
| `checking`  | `0`          | Disables condition validation                          |

## Features

### Item Viewer
- **Simple Item Viewer** -- select items from a dropdown to quickly preview how filter rules apply
- **Advanced Item Viewer** -- full control over item properties including group, type, base, rarity, name, item level, sockets, ethereal, identified, quality, superior mods, automods, pointmods, magic affixes (up to 3 prefixes and 3 suffixes), corruptions, and upgrades

### In-Game Settings
- Character level, class, and filter level (0-15)
- Difficulty selection (Normal, Nightmare, Hell, or Auto based on item level)
- Character location state: Shop, Equipped, Stash, Merc, or Ground
- Gold in stash and gold carried

### Filter Simulation
- Load filter files from disk or paste rules directly into the text area
- Support for two simultaneous filters for side-by-side comparison
- Randomized act background images for display context
- Auto-simulation on filter changes (can be toggled off)
- Condition validation with error reporting for unrecognized codes
- Cross-version detection (warns if PoD-specific codes are used in PD2 mode)

### Supported Filter Conditions
The simulator recognizes a wide range of filter conditions including:

- **Item properties**: NORM, EXC, ELT, NMAG, MAG, RARE, UNI, SET, ID, ETH, INF, SUP, RW, SOCK/SOCKETS, DEF, EDEF, EDAM, ED, MAXDUR, ILVL, QLVL, ALVL, CRAFTALVL, CRAFT, LVLREQ, QTY, PRICE, BUYPRICE, SELLPRICE
- **Character stats**: CLVL, DIFF/DIFFICULTY, FILTLVL/FILTERLVL, CHARSTAT, GOLD, CLASS (PD2)
- **Resistances and stats**: RES, FRES, CRES, LRES, PRES, AR, ARPER, FRW, IAS, FCR, FHR, FBR, MINDMG, MAXDMG, STR, DEX, LIFE, MANA, MFIND, GFIND, MAEK, DTM, REPLIFE, REPAIR, FOOLS, ALLSK
- **Equipment types**: ARMOR, WEAPON, HELM, CHEST, SHIELD, GLOVES, BOOTS, BELT, CIRC, and all weapon categories (AXE, MACE, SWORD, DAGGER, SPEAR, POLEARM, BOW, XBOW, STAFF, WAND, SCEPTER, etc.)
- **Class-specific codes**: DRU, BAR, DIN, NEC, SIN, SOR, ZON, plus class item codes (CL1-CL7, EQ1-EQ7, WP1-WP13)
- **Skill codes**: SK, CLSK, TABSK ranges for all classes
- **PD2-specific codes**: CHSK (skill charges), OS (oskills), MULTI (multi-layered stat conditions), PREFIX, SUFFIX, MAPID, MAPTIER, AMAZON/ASSASSIN/BARBARIAN/DRUID/NECROMANCER/PALADIN/SORCERESS, AUTOMOD, GEMMED, QUIVER, and various PD2 item codes
- **Location conditions**: SHOP, EQUIPPED, GROUND, INVENTORY, STASH, MERC
- **All individual item codes** for bases, gems, runes (including PD2-specific stacked runes and uber items)

### Display Keywords
Supports standard display formatting including color codes (%WHITE%, %GRAY%, %BLUE%, %YELLOW%, %GOLD%, %GREEN%, %DARK_GREEN%, %TAN%, %BLACK%, %ORANGE%, %PURPLE%, %RED%, %CORAL%, %SAGE%, %TEAL%, %LIGHT_GRAY%), item references (%NAME%, %PRICE%, %ILVL%, %ALVL%, %CRAFTALVL%, %LVLREQ%, %QTY%, %RANGE%, %WPNSPD%, %NL%, %CL%, %MAP%), and bracket escapes (%LBRACE%, %RBRACE%). Notification keywords (%NOTIFY-*%) are recognized and parsed but do not produce audio or visual notification effects in the simulator.

## Known Limitations
- Item notifications (%NOTIFY-*%) are parsed but do not produce sound or minimap icon effects
- Minimap icons (%MAP%) are recognized but not visually displayed
- Unique and set items cannot have their attributes fully customized (affix values are limited)
- Crafted items do not have predetermined affixes
- Wirt's Leg and quest weapons cannot be customized
- PRICE is not updated automatically when item properties change
- PREFIX, SUFFIX, and MAPID conditions are recognized as valid PD2 codes but are not fully simulated
- Item stats displayed may not be fully up to date with the latest PD2 season changes

## Project Structure

```
index.html           - Main application page and UI layout
data/
  simulation.js      - Core filter parsing, simulation engine, and UI logic
  custom_items.js    - Advanced item editing (affixes, corruptions, pointmods, etc.)
  items.js           - Item database (base items, uniques, sets, runewords)
  item_metadata.js   - Item type metadata, condition codes, and stat definitions
  item_affixes.js    - Affix data (magic prefixes/suffixes, corruptions, automods)
  styling.css        - Application styles
images/              - Background images (act screenshots), icons, and loading animations
```

## Reporting Issues

Please message Equa1ity on Discord with any issues, or open an issue on this repository.

## Credits
- Original Creator: BetweenWalls ([original repo](https://github.com/BetweenWalls/filterbird))
- Edits for the Launcher: Maaaark
- Launcher Integration: Equa1ity

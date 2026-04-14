# FilterBird - D2 Filter Simulator

PD2/PoD loot filter simulator. Static HTML app — vanilla JS, no npm, no build tools, no frameworks.

## Architecture

Files loaded via `<script>` tags in `index.html` in this order:
1. `data/items.js` — item base definitions (uniques, sets, bases)
2. `data/item_metadata.js` — `all_codes` validation table, item dimensions, runewords
3. `data/item_affixes.js` — prefix/suffix affix data
4. `data/simulation.js` — **core engine**: filter parser, formula evaluator, condition matcher, output renderer
5. `data/custom_items.js` — advanced item editor, property computation

## How Simulation Works

1. User selects item via `dropdown_item` or advanced editor
2. Item state stored in global `itemToCompare` object
3. `simulate()` → `parseFile()` iterates each `ItemDisplay[conditions]:output` line
4. Conditions parsed into a JS boolean expression string via complex for-loop (~line 1113)
5. **CRITICAL**: `eval(formula)` at line ~1242 executes the constructed expression. Any changes to condition string construction must be extremely careful about injection.
6. If match, output tokens replaced with values and rendered with D2 colors

## Version System

- `settings.version`: 0 = PoD (Path of Diablo), 1 = PD2
- `all_codes` values: 1 = PoD only, 2 = PD2 only, 3 = both versions

## Adding New Condition Codes

Checklist (all 4 required):
1. Add to `all_codes` in `item_metadata.js` (~line 1622) with version value (1/2/3)
2. If numeric, add to `nonbool_conditions` array in `simulation.js` (~line 1116)
3. Add output token handling in **both** places in `simulation.js`:
   - Validation: ~line 1275
   - Rendering: ~line 1505
4. If computed, add to `computeNewFilterProperties()` in `simulation.js`

## Key DOM Elements

- `filter_text_1` / `filter_text_2` — filter input textareas
- `dropdown_item` — item selector dropdown
- `output_1` / `output_2` — rendered item display
- `o1` / `o2` — filter error messages
- `o3` — debug output
- `o4` / `o5` — notices

## Testing

No automated tests. To test manually:
1. Open `index.html` in browser
2. Select an item from dropdown
3. Enter filter rules in textarea
4. Check output display and error messages
5. Version can be toggled via menu (PoD/PD2)

## Known Limitations

- CHSK/OS codes recognized but evaluate to 0
- PREFIX, SUFFIX, MAPID, AREALVL unimplemented
- STAT360 (corruption) partial
- Unique/set items can't be customized
- Many PD2 changes after s6 not implemented

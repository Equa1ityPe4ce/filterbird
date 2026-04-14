---
description: "Validate BH filter syntax offline. Use when: reviewing filter files, checking PRs that modify filter code, or helping users debug filter rules that aren't working."
user_invocable: true
---

# FilterBird Filter Validator

Validate BH filter code syntax without running the browser simulator.

## How to Validate

### Step 1: Read the filter text
Get the filter content from the user — either a file path or pasted text.

### Step 2: Load the validation data
Read `data/item_metadata.js` to get the `all_codes` object (starts around line 1622). This is the authoritative list of recognized condition codes and their version compatibility.

Also read the `nonbool_conditions` array from `data/simulation.js` (around line 1116) to know which codes require numeric comparisons.

### Step 3: Parse each rule
For each line matching `ItemDisplay[...]:`:

1. **Extract conditions** (between `[` and `]`)
2. **Extract output** (after `]:`)

### Step 4: Validate conditions

For each condition code in the brackets:

**Check code exists in all_codes:**
- If `all_codes[CODE]` is undefined, flag as unrecognized
- If `all_codes[CODE] == 1` and version is PD2, flag as PoD-only
- If `all_codes[CODE] == 2` and version is PoD, flag as PD2-only

**Check numeric conditions have operators:**
- If code is in `nonbool_conditions`, it MUST be followed by a comparison operator (`=`, `>`, `<`, `>=`, `<=`, `~`)
- Example: `ILVL` alone is invalid; `ILVL>=85` is valid

**Check for known unimplemented codes:**
- `CHSK`, `OS` — recognized but always evaluate to 0
- `AREALVL` (PoD only) — not implemented
- `PREFIX`, `SUFFIX`, `MAPID` (PD2 only) — not implemented
- Warn the user these won't work in the simulator

**Check logic operators:**
- `AND` and `OR` used together without parentheses is ambiguous — flag as warning
- Nested parentheses should be balanced

**Check STAT codes:**
- `STAT0` through `STAT504` are valid
- `CHARSTAT0` through `CHARSTAT500` are valid
- `MULTI<stat>,<layer>` format is valid
- `SK0` through `SK100`, `TABSK0` through `TABSK50`, `CLSK0` through `CLSK6` are valid

### Step 5: Validate output tokens

Known valid output tokens:
```
%NAME%, %ILVL%, %SOCKETS%, %RARITY%, %DEF%, %ED%, %AR%, %RES%,
%FRES%, %CRES%, %LRES%, %PRES%, %PRICE%, %BUYPRICE%, %SELLPRICE%,
%ALVL%, %CRAFTALVL%, %QLVL%, %LVLREQ%, %MAXDUR%, %QTY%,
%RUNENUM%, %RUNENAME%, %GOLD%,
%WIDTH%, %HEIGHT%, %AREA%, %MAXSOCKETS%, %BASEBLOCK%,
%REQLVL%, %REQSTR%, %REQDEX%, %UPDEX%, %UPSTR%, %UPLVL%,
%MAXRES%, %ALLATTRIB%,
%BASEMINONEH%, %BASEMAXONEH%, %BASEMINTWOH%, %BASEMAXTWOH%,
%BASEMINSMITE%, %BASEMAXSMITE%, %BASEMINTHROW%, %BASEMAXTHROW%,
%BASEMINKICK%, %BASEMAXKICK%,
%NL%, %LBRACE%, %RBRACE%, %CONTINUE%
```

Color tokens:
```
%WHITE%, %GRAY%, %BLUE%, %YELLOW%, %GOLD%, %GREEN%, %DGREEN%,
%TAN%, %BLACK%, %ORANGE%, %PURPLE%, %RED%, %TEAL%, %CORAL%,
%SAGE%, %LIGHT_GRAY%
```

Formula tokens:
- `$f(...)` — inline formula (validate parenthesis matching)
- `%FORMULA<KEY>%` — named formula reference (check `Formula[KEY]:` is defined)

### Step 6: Validate formula syntax

For `$f(...)` and `Formula[KEY]:` definitions:
- Check balanced parentheses
- Check function names are valid: `IF, AND, OR, MIN, MAX, FLOOR, CEIL, ROUND, MOD, AVERAGE, SQRT, POW, COUNT, COUNTIF, LN, EXP, XOR, ABS, SIGN`
- Check operators are valid: `+, -, *, /, ^, ==, !=, >=, <=, >, <`
- Variable names should be valid item/character properties or STAT codes

### Step 7: Report

Format output as:
```
ERRORS (will break):
- Line X: Unrecognized condition "FAKECODE"
- Line Y: Numeric condition ILVL missing comparison operator

WARNINGS (may not work as expected):
- Line Z: PD2-only code "PREFIX" used — not implemented in simulator
- Line W: AND/OR used together without parentheses

INFO:
- N rules parsed, M conditions checked
- Version: PD2/PoD
```

## Common Mistakes to Flag

1. `ItemDisplay[ILVL]: ...` — ILVL needs a value (e.g., `ILVL>=85`)
2. `ItemDisplay[SOCK 3]: ...` — Missing operator (should be `SOCK=3` or `SOCK>=3`)
3. `ItemDisplay[RARE AND MAG]: ...` — Item can't be both rare and magic
4. `ItemDisplay[]: %SOCKETS%` with no `%NAME%` — Item won't show name
5. Missing `Formula[KEY]:` definition when `%FORMULAKEY%` is used in output

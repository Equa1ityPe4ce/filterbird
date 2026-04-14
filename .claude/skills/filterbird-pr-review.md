---
description: "Domain-aware code review for FilterBird PRs. Use when: reviewing PRs that touch simulation.js, item_metadata.js, or custom_items.js — catches missing registrations, inconsistent code paths, and BH filter semantic errors."
user_invocable: true
---

# FilterBird PR Review

Review PRs touching filter code with domain-specific knowledge of BH filter semantics and FilterBird's architecture.

## Review Checklist

### 1. New Condition Codes

When a PR adds new condition codes, verify ALL FOUR registration points:

**a) `all_codes` in `data/item_metadata.js` (~line 1622)**
```
grep for the new code name in the all_codes object
```
- Verify version value: 1=PoD, 2=PD2, 3=both
- Check it matches the actual BH behavior

**b) `nonbool_conditions` in `data/simulation.js` (~line 1116)**
```
grep -n "nonbool_conditions" data/simulation.js
```
- If the code requires a numeric value (ILVL, DEF, WIDTH, etc.), it MUST be in this array
- If it's boolean (ETH, ID, NMAG), it should NOT be here

**c) Output token validation in `data/simulation.js` (~line 1275)**
```
Search for the output token processing section near "check output for invalid keywords"
```
- The new code must be recognized as a valid output token

**d) Output token rendering in `data/simulation.js` (~line 1505)**
```
Search for where output tokens are replaced with actual values
```
- The new code must map to the correct item property value

**e) Property computation in `data/simulation.js` or `data/custom_items.js`**
```
grep -n "computeNewFilterProperties" data/simulation.js data/custom_items.js
```
- If the property needs computation (not a direct item field), verify the logic

### 2. Formula System Changes

When a PR modifies the expression parser (lines ~720-974 in simulation.js):

**a) Tokenizer consistency**
- New operators must be tokenized correctly
- Check that operator precedence in the tokenizer matches the parser
- Verify unary vs binary disambiguation if +/- are involved

**b) Parser precedence**
- Verify the precedence chain: comparison < add/sub < mul/div < power < unary < atom
- `^` should be right-associative (recursive, not while-loop)
- New operators must be at the correct precedence level

**c) Function evaluation**
- New functions added to `evalFunc()` switch statement (~line 943)
- Function name added to the `funcs` array in tokenizer (~line 799)
- Edge cases handled (empty args, division by zero, NaN, Infinity)

**d) Variable resolution**
- `resolveFormulaVar()` handles the new variable type
- Check STAT, CHARSTAT, MULTI, and item property resolution

### 3. Item Data Changes

When a PR modifies item properties or metadata:

**a) Item dimension data**
```
grep -n "item_dimensions" data/item_metadata.js
```
- Verify dimensions match actual D2 game data
- Check per-item overrides (invwidth/invheight) vs type defaults

**b) Base definitions**
```
Check data/items.js for the item entry
```
- Verify stats (min/max damage, defense, requirements) match game data
- Check upgrade paths if UPDEX/UPSTR/UPLVL are affected

### 4. Duplicate Code Paths

FilterBird has known code duplication. When modifying one path, check the other:

- **Output token validation** (~line 1275) and **rendering** (~line 1505) — both must be updated
- **Condition parsing** in the for-loop (~line 1113) — check both PoD and PD2 branches
- **Item property initialization** in `computeNewFilterProperties()` and `loadCustomization()`

### 5. eval() Safety

The condition evaluator uses `eval(formula)` at line ~1242. Any PR that modifies how the formula string is constructed must be reviewed for:
- Characters that could escape the boolean expression
- New condition codes that produce unexpected characters
- Injection through item names or filter text

### 6. Version Compatibility

- New PD2-only codes must be `2` in `all_codes`, not `3`
- PoD-only codes must be `1`
- Codes common to both use `3`
- Check that version-gated warnings display correctly

## Common PR Issues

Based on past reviews (PR #15):

1. **COUNTIF semantics** — Must compare args against last arg, not count truthy values
2. **Unary operators** — Both tokenizer and parser must handle them
3. **Right-associativity** — `^` operator must use recursion, not a while loop
4. **Missing function** — Check the BH source for the complete function list (e.g., `SIGN` was missing)
5. **Variable resolution timing** — Should happen in parser (atom stage), not lexer (tokenizer)
6. **String consumption** — Parser must verify all tokens are consumed after parsing

## How to Run the Review

1. Get the PR diff: `gh pr diff <number>`
2. Identify which files changed and which checklist items apply
3. Read the relevant sections of each changed file
4. Cross-reference against this checklist
5. Report findings organized by severity:
   - **Blocking**: Missing registrations, incorrect semantics, eval safety
   - **Important**: Edge cases, missing error handling
   - **Minor**: Style, documentation, duplicate code

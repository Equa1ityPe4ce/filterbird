---
description: "Run filter rule smoke tests against FilterBird in the browser. Use when: testing filter changes, verifying formula evaluation, checking for regressions after code changes, or validating PR test plans."
user_invocable: true
---

# FilterBird Smoke Tester

Run filter rules against real items in FilterBird and verify the output matches expectations.

## Setup

Start the FilterBird server using Claude Preview:

```
preview_start with name "filterbird"
```

If the server isn't configured, create `.claude/launch.json` first:
```json
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "filterbird",
      "runtimeExecutable": "npx",
      "runtimeArgs": ["http-server", ".", "-p", "8080", "-c-1"],
      "port": 8080
    }
  ]
}
```

## Running Tests

For each test case, use `preview_eval` to:

1. **Set version** (PD2 or PoD):
   ```javascript
   document.getElementById('dropdown_version').value = 'PD2';
   setVersion(1); // 0=PoD, 1=PD2
   ```

2. **Select an item** by setting the dropdown:
   ```javascript
   document.getElementById('dropdown_item').value = 'ITEM_VALUE';
   setItem('ITEM_VALUE');
   ```

3. **Enter filter text**:
   ```javascript
   document.getElementById('filter_text_1').value = 'FILTER_RULES_HERE';
   ```

4. **Trigger simulation**:
   ```javascript
   simulate(1);
   ```

5. **Read results**:
   ```javascript
   JSON.stringify({
     output: document.getElementById('output_1').innerHTML,
     errors: document.getElementById('o1').innerHTML,
     debug: document.getElementById('o3').innerHTML,
     notices: document.getElementById('o4').innerHTML
   })
   ```

## Core Test Suite

Run these test cases to verify basic functionality. Check the output HTML for expected content.

### Boolean Conditions
| Test | Filter Rule | Item | Expected |
|------|------------|------|----------|
| Normal item match | `ItemDisplay[NMAG]: %NAME%` | Any normal item | Item name displayed |
| Magic item match | `ItemDisplay[MAG]: %BLUE% %NAME%` | Any magic item | Blue item name |
| Ethereal check | `ItemDisplay[ETH]: %NAME% (eth)` | Ethereal item | Name with (eth) |
| Socket check | `ItemDisplay[SOCK>2]: %NAME% %SOCKETS%s` | 3+ socket item | Name with socket count |

### Numeric Conditions
| Test | Filter Rule | Item | Expected |
|------|------------|------|----------|
| ILVL range | `ItemDisplay[ILVL>=85]: ilvl %ILVL%` | ilvl 85 item | "ilvl 85" |
| DEF check | `ItemDisplay[DEF>100]: def %DEF%` | High def armor | Defense value shown |

### New Condition Codes (PR #15)
| Test | Filter Rule | Item | Expected |
|------|------------|------|----------|
| WIDTH/HEIGHT | `ItemDisplay[]: %WIDTH%x%HEIGHT%` | Any item | Correct dimensions |
| AREA | `ItemDisplay[]: area=%AREA%` | Any item | WIDTH * HEIGHT |
| MAXSOCKETS | `ItemDisplay[]: max:%MAXSOCKETS%` | Socketable item | Max socket value |

### Formula System
| Test | Filter Rule | Expected Output |
|------|------------|-----------------|
| Inline formula | `ItemDisplay[]: $f(2+3)` | "5" |
| Named formula | `Formula[X]: ILVL * 2\nItemDisplay[]: %FORMULAX%` | Double the ilvl |
| COUNTIF | `ItemDisplay[]: $f(COUNTIF(1,2,1,3,1,1))` | "3" |
| Unary minus | `ItemDisplay[]: $f(-5+10)` | "5" |
| Unary NOT | `ItemDisplay[]: $f(!0)` | "1" |
| Nested functions | `ItemDisplay[]: $f(MAX(1,MIN(5,3)))` | "3" |
| Division by zero | `ItemDisplay[]: $f(1/0)` | "f_err" |

### Regression Checks
- Enter an empty filter — no crashes, no output
- Enter invalid syntax `ItemDisplay[FAKECODE]: test` — error message in o1
- Verify existing items still display correctly with standard filter rules

## Interpreting Results

- **output_1 innerHTML**: Contains the rendered item display. Check for expected text content and color spans.
- **o1 innerHTML**: Contains error messages. Should be empty for valid filters.
- **o3 innerHTML**: Debug info showing match results (true/false per rule).
- If output is empty and no errors, the filter didn't match the current item.

## Quick Smoke Test

Run this single command to verify FilterBird is working:

```javascript
document.getElementById('filter_text_1').value = 'ItemDisplay[]: %GREEN% TEST %NAME% ilvl=%ILVL%';
simulate(1);
document.getElementById('output_1').innerText;
```

Expected: Contains "TEST" followed by the current item name and ilvl value.

## `test --bugs` prints no bug section and no "none found" line

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-asyncstacks.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look the same.
- Workaround: took it as no bug.

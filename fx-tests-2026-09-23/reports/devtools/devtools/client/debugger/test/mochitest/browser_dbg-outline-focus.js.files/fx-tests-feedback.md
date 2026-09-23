## `test --bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-outline-focus.js --bugs`
- Expected: a Bugs section, with "none found" when there is none.
- Got: the same output as without `--bugs`, with no Bugs section, so "no bug" and "flag ignored" look the same.
- Workaround: took it as no bug.

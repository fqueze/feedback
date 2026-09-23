## `fx-tests test <path> --bugs` with no matching bug says nothing

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-editor-horizontal-scroll.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same summary as without `--bugs`, no mention of bugs at all, so "no bug" and "flag ignored" look the same.

## `fx-tests test <path> --bugs` shows no bug section

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-keyboard-navigation.js --bugs`
- Expected: the bugs naming the test, here bug 1932082 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-keyboard-navigation.js | single tracking bug", RESOLVED FIXED on 2026-09-11, RESOLVED INCOMPLETE before 2026-09-10), or an explicit "no bug names this test" line.
- Got: output identical to the plain `fx-tests test <path>` output, exit 0, no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: took the bug number from the `hg json-log` of the test file (the fix commit names it), then `mcp__moz__get_bugzilla_bug`.

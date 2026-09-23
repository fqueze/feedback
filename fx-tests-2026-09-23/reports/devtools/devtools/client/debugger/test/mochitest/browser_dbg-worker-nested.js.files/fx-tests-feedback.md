## `fx-tests test <path> --bugs` prints nothing about bugs when there is none

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-worker-nested.js --bugs`
- Expected: a "Bugs" section, even if it says "none open" (bug 1830985, RESOLVED INCOMPLETE, names this test).
- Got: the same output as without `--bugs`; no line at all about bugs, so "no bug" and "flag ignored" look identical.
- Workaround: Bugzilla REST search on the summary.

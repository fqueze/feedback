## `--bugs` with no matching bug says nothing

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-step-in-navigate.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the ordinary `test` output, with no bug section at all, so I could not tell whether it had searched.
- Workaround: Bugzilla REST quicksearch on the file name.

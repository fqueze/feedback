## `fx-tests test <path> --bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-sources-with-many-actors.js --bugs`
- Expected: a line like "Bugs: none name this test".
- Got: the normal `test` output with no bugs section at all, so it looks as if the flag was ignored.
- Workaround: took the absence to mean no bug.

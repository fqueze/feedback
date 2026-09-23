## `fx-tests test <path> --bugs` prints no bug section when there is none (browser_dbg-worker-scopes.js)

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-worker-scopes.js --bugs`
- Expected: a "Bugs" section, or a line such as "no bug names this test".
- Got: the same output as without `--bugs`, with nothing to show the flag was applied. "No bug" and "flag ignored" look the same.
- Workaround: took it as "no bug" and found the relevant bug (2070924, filed against DOM: Workers) from the git log of the code instead.

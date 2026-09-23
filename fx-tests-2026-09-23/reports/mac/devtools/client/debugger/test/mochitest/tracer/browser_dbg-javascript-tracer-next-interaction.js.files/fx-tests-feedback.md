## `--bugs` with no bug found prints nothing about bugs

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-next-interaction.js --bugs`
- Expected: a `Bugs` section, or a line such as "No sheriff-annotated bug names this test".
- Got: the same output as without `--bugs`, exit 0, nothing on stderr. It cannot be told apart from the flag being ignored or the live query failing silently.
- Workaround: ran it twice to make sure, then concluded "none".

## `--bugs` with no annotated bug prints nothing

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer.js --bugs`
- Expected: a line such as "Annotated bugs: none" when no sheriff-annotated bug names the test.
- Got: the same output as without `--bugs`, with nothing on stderr either. "No bug" and "the flag was ignored" look the same.
- Workaround: `--bugs --json`, then read `annotatedBugs` (`[]`).

## `fx-tests test <path> --bugs` with no bug prints nothing to say so

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-worker.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`; no sign the flag was honoured.
- Workaround: none needed, but I could not tell "no bug" from "flag ignored".

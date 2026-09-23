## `fx-tests test <path> --bugs` prints no bug section and no "no bugs found" line

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer.js --bugs`
- Expected: a Bugs section, or an explicit "no sheriff-annotated bug names this test".
- Got: the same output as without `--bugs`, ending at the Issues block. I could not tell "no bug" from "the lookup silently failed". (Bug 2070923 was filed for this test, but by a developer, not through sheriff annotation, so "none" was probably right.)
- Workaround: read the bug from Bugzilla directly.

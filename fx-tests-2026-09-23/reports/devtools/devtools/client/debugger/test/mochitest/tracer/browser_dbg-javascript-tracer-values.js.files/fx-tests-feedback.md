## `test --bugs` prints nothing when it finds no bug, and only looks at the last 7 days

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values.js --bugs`
- Question: which bug, if any, sheriffs starred this test's failures on.
- Expected: a Bugs section, saying "none" when there is none, and covering the window the failures are in.
- Got: the usual output with no Bugs section at all. Only stderr (`--progress`) shows it ranked annotations for 2026-09-16..2026-09-22, while all 5 failures are from 2026-08-31 to 2026-09-10, so a bug starred on them would have been missed too.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test name>`.

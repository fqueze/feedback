## `test --bugs` prints nothing when no bug matches, and misses the bug filed under the test's old path

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values-preview.js --bugs`
- Expected: a Bugs section listing bug 1959018 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-javascript-tracer-values-preview.js | single tracking bug", REOPENED), or at least a line saying no bug was found.
- Got: the usual test summary with no Bugs section and no "none found" line, so I could not tell "no bug" from "the flag did nothing". The bug names the test at its pre-move path (without `tracer/`), which a file-name match would have found.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_dbg-javascript-tracer-values-preview.js'`.

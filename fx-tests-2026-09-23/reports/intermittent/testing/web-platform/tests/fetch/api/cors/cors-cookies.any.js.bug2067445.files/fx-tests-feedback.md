# fx-tests feedback (cors-cookies.any.js.bug2067445)

## `fx-tests test` does not cover web-platform-tests
- Command: `fx-tests test testing/web-platform/tests/fetch/api/cors/cors-cookies.any.js`
- Expected: rates per config, history, failing tasks for a WPT test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — WPT is not in the data at all.
- Workaround: Treeherder `api/failuresbybug/?bug=N&tree=all` for annotated jobs; no denominator (runs) available, so no rate.

## `fx-tests task` omits WPT subtest failures
- Command: `fx-tests task Nfv4U3RIQRepLjW44J4XTg --profiles`
- Expected: `/fetch/api/cors/cors-cookies.any.html` listed under FAILED (it logged `TEST-UNEXPECTED-FAIL` for a subtest, which is what the sheriff starred).
- Got: "FAILED (9) — every test this job recorded a failure for" without it: the test-level status was OK, so a subtest-only failure is invisible.
- Workaround: grep the live_backing.log.

## `fx-tests intermittent --bug` hides that most annotations are off-trunk unexpected passes
- Command: `fx-tests intermittent --bug 2067445`
- Question: "what are the sheriff annotations on this bug, and are they all this failure?"
- Got: 2 trunk annotations in 7 days, both real failures. Treeherder has 369 annotations, 344 of them on beta/autoland/central being `TEST-UNEXPECTED-PASS ... expected FAIL` (metadata artifact) with no failure lines recorded. A line saying how many off-trunk / older annotations exist, and how many carry no failure line, would have surfaced this immediately.

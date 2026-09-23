## WPT tests: no data at all from `fx-tests test`
- Command: `fx-tests test testing/web-platform/tests/loading/early-hints/early-hints-response-time.h2.html`
- Expected: rates per config and failure modes, as for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only accepts xpcshell|mochitest.
- Workaround: Treeherder `api/failuresbybug/?bug=2007438` for annotated jobs, and the job logs for the values. No run count (denominator) was available anywhere.

## `fx-tests task` does not list a WPT subtest failure in a job whose test status is OK
- Command: `fx-tests task P1LdDfdhQu2k98bXk9_uVg --profiles`
- Expected: `/loading/early-hints/early-hints-response-time.h2.html` among the failures (the log has `TEST-UNEXPECTED-FAIL | ... | Interim response times should correspond to delays (h2)`, then `TEST-OK`).
- Got: the FAILED list names 7 other tests and not this one; same in the other three annotated jobs. The resource-usage profile does hold the `FAIL` marker for it.
- Workaround: grep the job log.

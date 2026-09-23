## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/mozilla/tests/focus/focus-next-tick-before-iframe-loaded-same-site.html` (also with `--history`)
- Expected: rates per config and history for a WPT test (or a message saying WPT is not covered).
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...", which reads as a misspelled path rather than "this harness is not indexed". `--harness` only accepts xpcshell|mochitest.
- Workaround: `fx-tests intermittent --bug <N>` for annotated jobs, then the job logs. No run count (denominator) is available, so no failure rate.

## `fx-tests task` on a WPT job lists expected results as failures and misses the unexpected subtest failure

- Command: `fx-tests task dQqwrM08SUqro0k0RsOQ_A --profiles` (and `--limit 0`)
- Expected: the job's unexpected results; here the only `TEST-UNEXPECTED-*` line in the log is a subtest FAIL of `/_mozilla/focus/focus-next-tick-before-iframe-loaded-same-site.html`.
- Got: "33 failing", all of them tests whose ERROR/TIMEOUT status is expected per metadata (pageswap, bluetooth, protocol-handler...), and not the test with the unexpected subtest FAIL (its harness status is OK).
- Workaround: `rg TEST-UNEXPECTED` over the job's live_backing.log.

# fx-tests feedback (ink-overflow-001-print.html)

## "How often does this WPT fail on its config, and since when?"

- Command: `fx-tests test testing/web-platform/tests/css/css-break/ink-overflow-001-print.html`
- Expected: rates per config and `--history` for a web-platform-tests test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (WPT not covered).
- Workaround: Treeherder REST API, `api/project/mozilla-central/jobs/?job_type_name=test-linux2404-64/debug-web-platform-tests-print-reftest-swr-nofis`, listing every run's result, then `fx-tests task` on each. About 10 extra calls.

## "What was the failure message of each annotated occurrence?"

- Command: `fx-tests intermittent --bug 1886146`
- Expected: the failure line (here `TEST-UNEXPECTED-PASS ... expected TIMEOUT`).
- Got: "Tests named, per annotated job (none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a test ...)". The line was TEST-UNEXPECTED-PASS, which the filter does not match.
- Workaround: looped `fx-tests task <id>` over all 17 occurrences. Its output says `UNEXPECTED-PASS` but not the expected status: the resource-usage profile's test marker has `Expected: TIMEOUT`, which `fx-tests task` could print.

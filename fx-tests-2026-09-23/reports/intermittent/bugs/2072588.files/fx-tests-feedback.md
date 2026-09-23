# fx-tests feedback (bug 2072588)

## Question: "which wdspec tests timed out in this job, and is the rate per config changing?"
- Commands: `fx-tests task NMPBKd9bT6GaJP5k8vcCBg --profiles`, `fx-tests test --help`
- Expected: per-test outcomes for a web-platform-tests-wdspec job, like for mochitest/xpcshell.
- Got: `0 tests, 0 executions, 0 failing` plus a warning that 2 failing markers "named no test path" (they do: `TIMEOUT /webdriver/tests/bidi/browsing_context/history_updated/history_updated.py`). `fx-tests test` only accepts `--harness xpcshell|mochitest`, so wdspec history/rates are unreachable.
- Workaround: Treeherder jobs API per push (th_jobs.py here) and the wpt_errorsummary.log artifacts.

## `fx-tests intermittent --bug 2072588` "Tests named" omits the TIMEOUT tests
- Expected: the tests behind the bug summary `TEST-UNEXPECTED-TIMEOUT | <test-path>` (645 TIMEOUT results across 191 of the 199 annotated jobs' errorsummaries).
- Got: "Tests named" led by `leakcheck` (98) and 21 wdspec tests with subtest failures; none of the TIMEOUTs is counted.

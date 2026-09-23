## Question: how often does this web-platform test fail, per config, and since when?

- Command: `fx-tests test testing/web-platform/tests/network-error-logging/sends-report-on-cache-validation.https.html` (also tried with `--history`)
- Expected: per-config pass/fail counts and a per-day history, as for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` — WPT is not covered at all, although `fx-tests intermittent --bug 2070935` knows the bug.
- Workaround: listed the jobs with the Treeherder jobs API (`job_type_name=test-android-em-14-x86_64-{ccov,shippable}/opt-geckoview-web-platform-tests-backlog-nofis-{1,2,3}`), downloaded 383 `public/test_info/wptreport.json` artifacts (~3 MB each) and extracted this test's status and duration with a script, then mapped push ids to revisions. That script found the step change (100% TIMEOUT on ccov since one central push), which nothing else would have shown. Cost: ~15 minutes and ~1 GB of downloads.
- What the output could have shown: the same verdict/per-config table/`--history` as for mochitest, built from `wptreport.json` (which has every test's status, subtest statuses and duration, passes included).

## Question: which test does each annotated job of a WPT bug name?

- Command: `fx-tests intermittent --bug 2070935`
- Got: `Tests named, per annotated job (none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a test ...)`. WPT failures here are `TEST-UNEXPECTED-TIMEOUT` lines, so every WPT bug reads as naming no test.
- Workaround: read the job logs / errorsummary. One of the 20 annotated jobs (GHA5PcjsRfmnn6KpUgYheA, chunk 3) did not run this test at all — a mis-star — which that section would have exposed.
- What the output could have shown: match any `TEST-UNEXPECTED-*` status, not only FAIL.

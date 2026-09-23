## Question: "did this job upload a per-test profile for my test, and what else failed?"

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles | grep -A4 test_breakpoint-14.js`
- Expected: this test's row (488 failing tests in the job).
- Got: nothing, because the FAILED list is truncated by default and the test was beyond the cut; needed `--limit 0`.
- Workaround: `--limit 0` into a file, then grep.
- Could have shown: a `--test <path>` filter on `fx-tests task`, or, when a job has hundreds of failures that all share one message, a grouped summary ("487 TIMEOUT, all 'Timed out and was force-killed'") instead of 488 rows.

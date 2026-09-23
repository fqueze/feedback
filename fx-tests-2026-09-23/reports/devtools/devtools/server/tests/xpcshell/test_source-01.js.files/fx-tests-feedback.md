## Was this test's failure part of a job-wide collapse?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_source-01.js --task-ids --limit 0`
- Expected: some sign, next to each task ID, that the job it failed in had hundreds of other failures (here 477 to 1564 TIMEOUTs per job, every one of the 6).
- Got: only the task IDs and config. "6x TIMEOUT Test exceeded time limit" reads like a slow test. I had to run `fx-tests task <id>` six times to find out every one of them was a job where everything timed out.
- Workaround: `fx-tests task <id> --profiles` per job and read its "Outcomes" line.
- What would have answered it: a per-task failure count ("of 1472 tests, 1061 failing"), or a verdict line like "all 6 failures are in jobs where >50% of tests failed".

## The test's own entry in `fx-tests task` output

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: to find the entry for the test I am diagnosing.
- Got: the first 20 of 488 failures, alphabetically, so my test was not shown. I needed `--limit 0` (2,000 lines) and then a grep.
- Workaround: `--limit 0` and grep.
- Suggestion: a `--test <path>` filter on `fx-tests task`, or always show the tests passed in with `fx-tests test --task-ids` first.

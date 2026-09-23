## Question: "this test's row in that job"

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles` (a job with 1,061 failing tests).
- Expected: a way to see the one test I am diagnosing in that job (its status, message, profile, retry).
- Got: the first 20 failures and `… 1041 more (--limit 0 for all)`; my test was not among them.
- Workaround: `--limit 0` into a file, then grep. A `--test <path>` filter on `fx-tests task` would answer it directly.


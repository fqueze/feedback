## Question: "what happened to my test in this job?"

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to see one test's row in a job with 1061 failures.
- Got: the list is truncated at ~20 rows (`… 1041 more`), there is no `--test <path>` filter, so my test was not shown.
- Workaround: `--limit 0 --full-messages` to a file, then grep for the path.

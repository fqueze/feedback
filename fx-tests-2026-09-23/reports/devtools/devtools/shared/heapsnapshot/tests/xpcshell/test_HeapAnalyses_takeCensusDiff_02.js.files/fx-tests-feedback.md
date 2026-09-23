## `fx-tests task` has no way to show one test's entry in a huge job

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles 2>&1 | grep -A3 takeCensusDiff_02`
- Expected: the entry for my test (outcome, retry result, per-test profile or its absence).
- Got: nothing; the FAILED list (1,154 entries) is truncated by default, so the test was not in the output.
- Workaround: rerun with `--limit 0` into a file and grep it.
- Question the output could answer: "what does this job say about test X". A `--test <path>` filter on `fx-tests task` would answer it directly.

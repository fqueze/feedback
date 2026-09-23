## `task --profiles` truncates the failure list, so a test's row is not found

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Question: this one test's outcome and profiles in a job where 1061 tests failed.
- Got: the first 20 failing tests, without mine; needed `--limit 0` and a grep over 1061 rows.
- Would help: a `--test <path>` filter on `fx-tests task`.

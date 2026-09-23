## Question: "this one test's outcome in this job"

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to see the row for `devtools/server/tests/xpcshell/test_breakpoint-16.js` in a job with 1061 failing tests.
- Got: the first 15 failing tests, alphabetically; mine was not among them.
- Workaround: `--limit 0` into a file, then grep, once per job (six jobs).
- What could have answered it: a `--test <path>` filter on `fx-tests task`, or `fx-tests test <path> --task-ids` printing the per-job message and execution count next to each task ID.

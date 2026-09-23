## Question: "this one test's outcome in this job" (`fx-tests task`)

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to see the entry for `devtools/server/tests/xpcshell/test_breakpoint-24.js` in a job with 1061 failing tests.
- Got: the first 20 failing tests, then `… 1041 more (--limit 0 for all)`; the test was not among them.
- Workaround: `--limit 0` and grep, per job (6 jobs, ~1000 entries each).
- Could have shown: a `--test <path>` filter on `fx-tests task`, or a line under `fx-tests test <path> --task-ids` saying whether a per-test profile exists for each listed task.

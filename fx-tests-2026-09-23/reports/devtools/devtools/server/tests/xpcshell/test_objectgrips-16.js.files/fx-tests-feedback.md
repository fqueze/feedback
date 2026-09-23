## Question: this one test's outcome in a job where hundreds of tests failed

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to see the row for `devtools/server/tests/xpcshell/test_objectgrips-16.js` (outcome, message, profile) in this job.
- Got: the first 20 of 488 failing tests, alphabetically, then `… 468 more (--limit 0 for all)`. Mine was not among them, and there is no `--test <path>` filter.
- Workaround: went straight to the resource-usage profile with `profiler-cli thread markers --search objectgrips-16`. `--limit 0` and grep would also work, at the cost of ~500 rows of output per job, six jobs.
- What would have answered it: `fx-tests task <id> --test <path>` (or a path substring filter), printing just that test's executions.

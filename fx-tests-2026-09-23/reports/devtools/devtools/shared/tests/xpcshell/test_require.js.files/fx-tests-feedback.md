## Question: "what happened to my test in this job" when the job has ~1000 failures

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to see the row for `devtools/shared/tests/xpcshell/test_require.js` (the test I came from).
- Got: the first 18 failing rows alphabetically, then `… 1041 more (--limit 0 for all)`; my test was not among them.
- Workaround: `--limit 0 --passed` into a file, then `rg` for the path (4,000+ lines per job).
- Could show: a `--test <path>` filter, or when the job has hundreds of failures, a one-line summary up front ("1057 TIMEOUTs, all started between t=45s and t=55s"), which is the actual answer for a mass-failure job.

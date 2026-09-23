## Question: this one test's row in a job where 1000+ tests failed

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: to find test_getRuleText.js's outcome, message and per-test profile (or its absence) in the job.
- Got: the first 20 of 1061 failing rows, alphabetical, and `… 1041 more (--limit 0 for all)`; no option to restrict to one test path.
- Workaround: `COLUMNS=300 fx-tests task <id> --profiles --limit 0 --messages > file; grep -A5 test_getRuleText.js$ file`, for each of the 6 tasks. A `--test <path>` filter (or `fx-tests test <path> --task-ids` printing the per-task message and profile URL) would answer it directly.

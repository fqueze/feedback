# fx-tests feedback (test_blackboxing-09.js)

## "What happened to my test in this job?"

- Command: `fx-tests task FADMjPT5QXm-7tdUJVLRRQ --profiles` (and the same for PyUxuO, Q6pDc4, LKys8j)
- Expected: a way to see the one test I am diagnosing in a job where 1,500 tests failed.
- Got: the first 20 failing tests alphabetically, then `… 1544 more (--limit 0 for all)`; my test is not among them, and `task` has no `--test <path>` filter.
- Workaround: `--limit 0` into a file and grep, or load the resource-usage profile and search its markers.
- What would help: `fx-tests task <id> --test <path>`, printing that test's executions, status, messages and profile URLs.

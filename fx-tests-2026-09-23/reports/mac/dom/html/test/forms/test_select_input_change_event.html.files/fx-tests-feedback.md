## Question: all the failing tasks of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --task-ids --messages --full-messages --profiles --limit 0`
- Expected: with `--limit 0`, every task ID (8 jobs, 16 rows) and every profile URL for the test's row.
- Got: 5 task rows then `… 11 more tasks`, and 5 profile pairs then `… 3 more profiles`, even with `--limit 0`. Duplicate rows per task (one per failed run) also eat the 5-row budget, so only 3 distinct tasks show.
- Workaround: `--json` and reading `permaFails[].taskIds` / `.profiles` with a script.
- What the output could show: the distinct task IDs, one line each with job name and the number of failed runs in it; `--limit 0` honoured.

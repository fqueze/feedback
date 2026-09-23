## Question: "this test's outcome and message in each of its failing jobs"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_getTextAtLineColumn.js --task-ids --limit 0`, then `fx-tests task <taskId> --profiles --limit 0 --messages | rg -A4 test_getTextAtLineColumn` once per job (6 times).
- Expected: the task-id list to carry each job's message for this test, or `fx-tests task` to take a test filter (`--test <path>`), so one command answers it.
- Got: task IDs only; `fx-tests task` prints every failing test of the job (1000+ entries in these jobs). My first try, without `--limit 0`, printed nothing for this test: it is past the default limit, and the `… more` line was filtered out by my `rg`.
- Also: the same failure is labelled differently per job. JOcVul and LKys8j say `Test timed out` (the `test` marker's message), the other four say `... | Timed out and was force-killed by the harness; ...` (the replayed ERROR). Every job has both messages, so the "first message" pick is not stable across jobs.

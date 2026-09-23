## Question: the task IDs and per-test profile URLs of every failing job for one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --messages --task-ids --profiles --full-messages --limit 0`
- Expected: `--limit 0` to expand the per-test "… 11 more tasks" and "… 3 more profiles" lines, so all 8 jobs' task IDs and test profile URLs are listed.
- Got: the lists stay truncated at 5 whatever `--limit` is; `--limit` only seems to apply to the rows.
- Also: `fx-tests try <rev> --test <path> --task-ids --profiles --messages` prints only the per-config ran/failed table and silently ignores `--task-ids`, `--profiles` and `--messages`, although that is the one-test view where they would be most useful.
- Workaround: `--json`, then `permaFails[i].taskIds` / `.profiles`. The per-test `-2` (retry) profile URLs were not in the text output either; `fx-tests task <taskId> --profiles` lists them.

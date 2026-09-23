## Question: which task IDs ran this test on the try push (to open a passing run's profile)?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_webconsole_filters.js --all-jobs --profiles --task-ids` (and the same with `--json`)
- Expected: with `--task-ids`/`--profiles`, the task IDs (and resource-usage profile URLs) behind each row of the per-config table, so a passing run of a previously skipped test can be checked (did it run, how long did it take).
- Got: only the per-config counts; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: Treeherder API (`/api/project/try/push/?revision=...`, then `/api/jobs/?push_id=...`) filtered by job name, then `fx-tests task <taskId>`.

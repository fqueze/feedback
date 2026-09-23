## Question: which tasks ran this test on a try push (to open one where it passed)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/framework/test/browser_destroying_iframes.js --all-jobs --task-ids --profiles` (also with `--json`)
- Expected: per config, the task IDs (and resource-usage profile URLs) of the jobs that ran the test, since `--task-ids`/`--profiles` were passed.
- Got: only counts per config (jobs / passed / passed on retry / failed); `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: queried the Treeherder REST API (`/api/project/try/push/?revision=...`, then `/api/jobs/?push_id=...`) for the job names listed, then `fx-tests task <id>` to get the resource-usage profile URL.
- What the output could have shown: a task ID (and resource-usage profile URL) per passing job run under each config row.

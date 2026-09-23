## Question: which tasks ran this test on config X (to open the job that passed it)?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/test/browser_toolbox_split_console.js --task-ids` (and the same with `--json`)
- Expected: the task IDs (with retry ids) behind each config row's `jobs` / `passed` counts, since `--task-ids` was accepted.
- Got: the per-config table only. `--task-ids` was silently ignored, and the JSON has no task IDs either.
- Workaround: the Treeherder `/api/jobs/?push_id=` endpoint filtered on `job_type_name`, then `fx-tests task <id> --passed` on each one to confirm the test ran in that job.
- What the output could have shown: one `task <id>.<run>  passed|failed` line per job under each config row when `--task-ids` is given.

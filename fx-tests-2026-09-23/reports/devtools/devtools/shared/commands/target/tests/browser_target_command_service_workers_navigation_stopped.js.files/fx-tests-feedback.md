## Question: the task IDs of the jobs that ran one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/shared/commands/target/tests/browser_target_command_service_workers_navigation_stopped.js --all-jobs --task-ids --profiles`
- Expected: with `--task-ids` / `--profiles`, the task IDs (and resource-usage profile URLs) of the job runs counted in each row, so a passing run on a formerly skipped config can be opened and cited.
- Got: only the per-config counts table (jobs / passed / passed on retry / failed); `--task-ids` and `--profiles` are silently ignored, and `--json` has no task IDs either.
- Workaround: look the task IDs up on Treeherder separately.


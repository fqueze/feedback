## review-browser_dbg-preview-wrapped-lines.js: task IDs of the try jobs where the test passed

- Question: which tasks ran this test on the try push (to open a passing run's log)?
- Command: `fx-tests try 2888bcab0070 --test devtools/client/debugger/test/mochitest/browser_dbg-preview-wrapped-lines.js --all-jobs --task-ids` (and with `--json`)
- Expected: a task ID per job counted in the per-config table.
- Got: the same per-config counts; `--task-ids` silently ignored, and the JSON has no task IDs either.
- Workaround: Treeherder `api/jobs/?push_id=...` filtered on `job_type_name`, then the Taskcluster log.

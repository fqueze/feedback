## Question: which jobs of a try push ran this (skipped-elsewhere) test and passed?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/server/tests/browser/browser_storage_dynamic_windows.js --task-ids` (also with `--json`)
- Expected: the task IDs behind each configuration row (jobs/passed/failed), so the passing runs' resource-usage profiles can be opened to check the test's duration on the config its `skip-if` targeted.
- Got: only per-config counts; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: Treeherder API (`/api/project/try/push/?revision=`, then `/api/jobs/?push_id=`) filtered by job name.

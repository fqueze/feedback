## Question: which tasks ran this test on a try push (to open a passing run's resource-usage profile)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/rules/test/browser_rules_authored_color.js --task-ids` (also with `--json`)
- Expected: the task IDs behind each jobs/passed/failed count, as `--task-ids` gives for failures.
- Got: only per-config counts; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=`, then `/api/jobs/?push_id=`) filtered by job name, then `fx-tests task <id> --passed`.

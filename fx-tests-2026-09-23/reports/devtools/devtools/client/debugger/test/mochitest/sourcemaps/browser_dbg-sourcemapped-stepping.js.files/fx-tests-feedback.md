## Question: the task IDs of the passing jobs that ran this test on a try push (review)

- Command: `fx-tests try 2888bcab0070 --test devtools/client/debugger/test/mochitest/sourcemaps/browser_dbg-sourcemapped-stepping.js --all-jobs --task-ids` (and the same with `--json`)
- Expected: with `--task-ids`, the task ID behind each counted job per config, so one passing run can be opened (`fx-tests task <id> --profiles`) to check the test really ran.
- Got: the same per-config pass/fail table with no task IDs; the `--json` has only counts per `jobName`.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=…`, then `/api/jobs/?push_id=…`), filtering on `job_type_name`.

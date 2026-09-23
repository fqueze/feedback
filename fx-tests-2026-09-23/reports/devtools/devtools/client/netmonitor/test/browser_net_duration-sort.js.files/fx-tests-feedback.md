## Question: which tasks ran this test and passed, on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_duration-sort.js --task-ids` (also `--json`)
- Expected: the task IDs behind each "passed" count, so the passing run's resource-usage profile can be opened (the only profile a passing run leaves).
- Got: the per-config ran/pass/fail table only; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either. `--config` is refused on `try`, and `fx-tests task` on the job does not list passing tests.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=` then `/api/jobs/?push_id=`), filtered on the job names from the table, then `fx-tests task <id> --profiles`.

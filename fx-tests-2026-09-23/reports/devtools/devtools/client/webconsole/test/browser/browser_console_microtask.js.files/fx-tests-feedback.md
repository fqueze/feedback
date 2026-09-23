## Which jobs ran this test on a try push, and did it pass in each

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_microtask.js --task-ids --profiles` (and the same with `--json`)
- Expected: next to each config's jobs/passed/failed counts, the task IDs (and resource-usage profile URLs) of the job runs that ran the test, so a "passed N times" claim can be linked and spot-checked.
- Got: only the per-config count table; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: took a task ID for the same chunk from another test's failure rows in `fx-tests try <rev> --task-ids`, then `fx-tests task <id> --passed --limit 0` to see this test's PASS row.

## (review) The task IDs of one configuration's jobs on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --config windows11-64-25h2-asan/opt-mochitest-devtools-chrome-2 --task-ids --limit 0`
- Expected: the task IDs of the 3 Windows asan chrome-2 jobs, to open one's resource-usage profile and spot-check a PASS that `--all-jobs --test` counted.
- Got: `--config cannot be applied to try`. The unfiltered output only lists task IDs next to failures, and none of this config's failures was in the truncated task lists.
- Workaround: Treeherder `api/jobs/?push_id=…`, filtered by job type name.

# fx-tests feedback

## Question: which task ran this (passing) test on config X?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/server/tests/browser/browser_accessibility_walker.js --all-jobs --task-ids --profiles` (also with `--json`)
- Expected: the task IDs (and resource-usage profile URLs) of the job runs counted under `passed`, so I can open one and confirm the test ran in manifest order with its neighbours.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: `curl https://treeherder.mozilla.org/api/jobs/?push_id=<id>&count=2000` and filter by `job_type_name` to get the task IDs, then `fx-tests task <id> --passed`.
- What the output could have shown: one task ID per job run in each row, like the failure sections do.

## Minor: job counts differ between runs with and without --all-jobs

- `fx-tests try <rev> --test <path>` reported `test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-3` with 4 jobs; the same command with `--all-jobs` reported 3 jobs for it. Unclear which is right (a retried run counted twice?).

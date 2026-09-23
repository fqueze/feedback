## Question: the task IDs of the passing jobs that ran one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/test/browser_inspector_reload_nested_iframe.js --task-ids --limit 0` (and the same with `--json`)
- Expected: next to each configuration's pass/fail counts, the task IDs (with run numbers) of the jobs that ran the test, so their resource-usage profiles can be loaded to check how close a passing run came to the old failure (the test used to fail on a timing margin).
- Got: only per-config counts; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: look up the task IDs on Treeherder / Taskcluster separately.

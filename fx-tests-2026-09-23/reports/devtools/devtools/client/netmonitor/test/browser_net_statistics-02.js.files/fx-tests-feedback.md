## Question: which task is the job run where this test passed?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_statistics-02.js --task-ids` (also with `--json`)
- Expected: the task IDs behind each count, in particular the one passing run on `test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-1` (1 passed, 2 failed), to open its resource-usage profile and compare with the failing ones.
- Got: per-config counts only; `--task-ids` is ignored with `--test`, and the JSON has no task IDs either.
- Workaround: looked the job up through the Treeherder API.

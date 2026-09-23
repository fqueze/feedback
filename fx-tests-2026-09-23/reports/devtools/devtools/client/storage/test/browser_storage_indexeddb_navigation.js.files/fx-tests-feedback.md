## Question: which task IDs ran this test, and passed, on a try push (to confirm a skip-if removal from a profile)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/storage/test/browser_storage_indexeddb_navigation.js --task-ids --profiles` (also with `--json`)
- Expected: the per-config ran/pass table, plus the task IDs (and resource-usage profile URLs) of the jobs that ran the test, since `--task-ids --profiles` were given.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored in `--test` mode, and the JSON has no task IDs either.
- Workaround: looked the task IDs up via the Treeherder API to open one passing job's resource-usage profile.

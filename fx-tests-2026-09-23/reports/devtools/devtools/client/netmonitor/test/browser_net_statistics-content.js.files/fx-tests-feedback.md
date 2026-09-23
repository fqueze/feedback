## `fx-tests try --all-jobs --test <path> --task-ids` prints no task IDs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_statistics-content.js --task-ids` (also with `--json`)
- Question: which tasks ran this test and passed (to open their resource-usage profiles and read the test's duration against its timeout).
- Expected: the task IDs behind each per-config row (the `--task-ids` flag is accepted without error).
- Got: the per-config table only (jobs / passed / passed on retry / failed); neither text nor JSON carries task IDs.
- Workaround: looked the task IDs up with treeherder-cli.

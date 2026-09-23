## `fx-tests try --test` ignores `--task-ids`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/grids/test/browser_grids_grid-list-on-mutation-element-added.js --task-ids --limit 0` (also tried `--json`)
- Question: which task ran this test on each config, so I can open one passing job (`fx-tests task <id> --passed`) or its resource-usage profile and confirm it ran rather than was skipped.
- Expected: task IDs per config row, as `--task-ids` gives elsewhere.
- Got: only the per-config counts table (jobs / passed / passed on retry / failed); the JSON has no task IDs either.
- Workaround: grepped the `--all-jobs --task-ids` failure listing for a task of the same config/chunk that had failed for another test. That only works when another test failed in that chunk; for chunks where nothing failed there was no way to get a task ID.

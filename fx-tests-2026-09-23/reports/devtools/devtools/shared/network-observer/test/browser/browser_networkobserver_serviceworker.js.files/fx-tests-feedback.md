## The task IDs of one test's failures on one config of a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --profiles --limit 0` (row for `browser_networkobserver_serviceworker.js`), then `... --config windows11-64-25h2/opt-mochitest-devtools-chrome-2`.
- Expected: every task ID of the row, or a way to narrow the row to one config (I wanted a Windows profile).
- Got: `--limit 0` still prints 5 tasks and "… 37 more tasks" per row (the limit applies to rows, not to the tasks within one); `--config` is refused for `try`. `--test <path> --all-jobs --task-ids` gives the per-config table but no task IDs.
- Workaround: `--json` and a script over `permaFails[i].taskIds`, which has all 42 with job names.
- What could show it: `--limit 0` also expanding the per-row task list, or `--test <path> --task-ids` listing task IDs per config row.

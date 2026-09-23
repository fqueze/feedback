# fx-tests feedback (browser_net_persistent_logs.js)

## Question: which tasks ran this test on a given config of a try push, so I can read their resource-usage profiles?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_persistent_logs.js --task-ids`
- Expected: per config, the task IDs of the jobs counted in the passed/failed columns (the test passed everywhere, so the task IDs are the only way to its profiles).
- Got: the per-config table only; `--task-ids` is silently ignored, and `--json` has no task IDs either.
- Also tried: `fx-tests try <rev> --task-ids --limit 0` — rows still end in `… 3 more configs` and the failing win debug dt7 tasks were not findable by config name; `--config` is refused on `try`.
- Workaround: `treeherder-cli <rev> --filter <job name> --match-filter all --json` for the job ids, then `https://treeherder.mozilla.org/api/project/try/jobs/<id>/` for each `task_id`, then `fx-tests task <taskId> --json` to confirm PASS. Four tools for one question.

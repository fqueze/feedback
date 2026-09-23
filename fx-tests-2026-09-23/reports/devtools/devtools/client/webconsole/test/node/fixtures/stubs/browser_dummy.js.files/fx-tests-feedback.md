## The task IDs of the jobs where a test passed on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --harness mochitest --test devtools/client/webconsole/test/node/fixtures/stubs/browser_dummy.js --all-jobs --task-ids` (also with `--json`)
- Expected: per configuration, the task IDs of the job runs that ran the test, so one can open `fx-tests task <id> --profiles` and link the passing run's resource-usage profile ("it ran and passed here").
- Got: only per-config counts (jobs/passed/passed on retry/failed); `--task-ids` is silently ignored in `--test` mode, text and JSON alike.
- Workaround: look the task up via the Treeherder API / treeherder-cli by job name.

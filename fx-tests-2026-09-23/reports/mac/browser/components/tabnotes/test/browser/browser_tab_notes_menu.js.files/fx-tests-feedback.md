## Which subtest (add_task) a "Test timed out" happened in

- Question: of a browser-chrome test's 29 timeouts, which `add_task` was running when each hit. A test with many tasks usually times out in more than one of them, for different reasons, and the per-task split is what tells the failure modes apart.
- Commands: `fx-tests test <path> --task-ids --issue 1`, `fx-tests task <id> --messages`, `fx-tests task <id> --json`.
- Expected: the name of the task running at the timeout (the last `Entering test <name>` before `Test timed out`), e.g. as `Test timed out (in test_openTabNotePanelFromContextMenu)`, so the Issues block splits by task.
- Got: `Test timed out` only, identical for every task; the task name appears nowhere in the output or the JSON.
- Workaround: load every failing per-test profile with profiler-cli and read the last `Entering test` marker — one 0.6 GB load per failure.

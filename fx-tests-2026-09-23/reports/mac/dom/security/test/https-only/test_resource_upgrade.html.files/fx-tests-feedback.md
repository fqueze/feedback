## Question: how often does each failure mode fail, per config?

- Command: `fx-tests test <path>` prints a per-config table that merges every failure mode. `fx-tests test <path> --issue 3` is refused ("--issue ... needs --task-ids").
- Expected: the per-config table (fails / runs / rate) for a single issue row, since this test has two unrelated modes (137 TIMEOUT, 64 websocket FAIL) with different config profiles (debug-heavy vs. opt-heavy).
- Got: nothing per mode, except the task list under `--issue N --task-ids --limit 0`.
- Workaround: a Python script over the `--issue N --task-ids --limit 0` text to count executions (the `×2` suffix) per config and per day.
- Could have shown: `--issue N` also filtering the Failing configurations table and the `--history` table.

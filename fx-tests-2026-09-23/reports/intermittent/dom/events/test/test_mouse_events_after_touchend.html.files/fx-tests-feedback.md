## Question: what are this test's failure modes, and how often each?

- Command: `fx-tests test dom/events/test/test_mouse_events_after_touchend.html` (also `--task-ids --json`)
- Expected: the Issues block to list the real failing assertions ("Single tap should not cause mouse events if touchend is consumed ...", "Multiple touch should not cause mouse events ...") with counts.
- Got: one issue, 182x `FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: ...`. That is the `SimpleTest.todo()` SimpleTest.finish() emits for any test calling `requestFlakyTimeout`, not a failure. It is chosen as "first failure per run" in all 182 runs, and the per-task `message` in `--json` is the same, so the real failure modes are invisible at the test level. `fx-tests task <id> --messages` does show the real assertion.
- Workaround: running `fx-tests task <id> --messages` per task to classify failure modes.
- Could have shown: skip the requestFlakyTimeout TODO line (it is emitted by every such test at finish, pass or fail) when picking the first failure message.

Addendum: the bug's log (bug 2031660 comment 0) shows why that message is picked. In a failing run the harness replays the buffered log and prints the todo as `TEST-FAIL | ... | The author of the test has indicated that flaky timeouts are expected.` (a known failure, not `TEST-UNEXPECTED-FAIL`); in the per-test profile the same message is a `TEST-KNOWN-FAIL` marker. Keying "first failure" on `TEST-UNEXPECTED-*` would give the real assertion.

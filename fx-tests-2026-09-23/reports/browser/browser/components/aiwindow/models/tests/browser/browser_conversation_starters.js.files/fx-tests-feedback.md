## Question: which subtest (add_task) failed, in each failing job of one test?

- Command: `fx-tests task <taskId> --profiles --messages` and `fx-tests task <taskId> --json` (fields `failures[].messages`, `allMessages[].message`).
- Expected: the subtest the "Uncaught exception in test" happened in (the log's `TEST-UNEXPECTED-FAIL | <path> | <subtest> - ...`), so I can tell whether all failures hit the same step.
- Got: only the message text, no subtest name or stack. The test has ~30 tasks and the same message can come from any of them.
- Workaround: loaded each per-test profile and read the Test markers.

## Question: a passing job of the same config, to compare its timeline with a failing one

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: some way to get task IDs of passing runs (e.g. `--passing` or with `--coverage`).
- Got: failing task IDs only.
- Workaround: used the harness retry inside the failing job, which passed, as the comparison.

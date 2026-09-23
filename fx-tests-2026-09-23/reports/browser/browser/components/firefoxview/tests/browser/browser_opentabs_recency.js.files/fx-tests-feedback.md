## Which subtest failed, per failure

- Command: `fx-tests test browser/components/firefoxview/tests/browser/browser_opentabs_recency.js --task-ids --limit 0`
- Question: which `add_task` fails in each of the 20 failures (the same message is thrown by a helper called from 4 subtests).
- Expected: the message as the harness logs it, `test_windows_activation - Uncaught exception in test - ...`.
- Got: `Uncaught exception in test - Waiting for tab list ...` with the subtest prefix stripped, while `fx-tests intermittent --bug 1875877 --since 21` does show `test_windows_activation - Uncaught exception ...`.
- Workaround: load per-test profiles one by one and read the `Entering test` markers.

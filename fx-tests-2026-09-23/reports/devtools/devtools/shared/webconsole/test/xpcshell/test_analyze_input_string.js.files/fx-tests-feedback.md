## Question: how did this failing execution end, when it has no message?

- Command: `fx-tests test devtools/shared/webconsole/test/xpcshell/test_analyze_input_string.js` and `fx-tests task RbTuzuWHTxaPqzDe1fpZ0w.0 --profiles`
- Expected: something that distinguishes "the test ran and failed" from "the process died at launch": the duration of the failing execution (here `took 46ms`, against ~2 s for its neighbours), and whether any output was replayed.
- Got: `Failure details not recorded (likely Android or platform logging issue)` — the run was desktop Windows ccov, where the xpcshell harness's retry path simply logs no message and no return code. `task` says "No failing test named a per-test profile" and "Passed when the harness reran it", but not the duration.
- Workaround: loaded the resource-usage profile and read the `test` marker's duration and the absence of a `Begin of full log` marker; grepped live_backing.log.
- What could have shown it: the failing execution's duration in `task` and in `test --task-ids`, and a wording for empty messages that does not presume Android.

## Question: why did this test fail, when "Failure details not recorded"?

- Command: `fx-tests test devtools/shared/network-observer/test/xpcshell/test_security-info-static-hpkp.js` and `fx-tests task X6xclY0wQlmVAYP3ZZZU9g --messages`
- Expected: some reason for the failure, or a pointer to the harness line that explains it.
- Got: `test` shows "Failure details not recorded (likely Android or platform logging issue)", and `task --messages` shows only "Passed when the harness reran it. / Failed only in the parallel phase."
- Workaround: I loaded the resource-usage profile and listed the INFO markers inside the test's `test` marker interval. That turned up `remotexpcshelltests.py | Failed to start process: Only one instance of an application may be running at once`. The line does not name the test, so nothing matched by name.
- Suggestion: for a failing test with no message, `task` could show the harness `Failed to start process`, `Could not read log file` and `Timing out` INFO lines logged inside that test's interval.

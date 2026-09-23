## Question: "what was the failure message?" for an Android xpcshell failure
- Command: `fx-tests test devtools/shared/network-observer/test/xpcshell/test_throttle.js`
- Expected: the failure mode.
- Got: `Failure details not recorded (likely Android or platform logging issue)`.
- The job's resource-usage profile has the cause as INFO lines on the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`, followed by `<test> failed or timed out, will retry.`. When there is no TEST-UNEXPECTED line, fx-tests could fall back to the last `Failed to start process` / `Timing out` / `Could not read log file` INFO line for the test.

## Question: "which other tests failed with the same harness message?"
- Command: `fx-tests failures --harness xpcshell --message "Failure details not recorded"`, and `fx-tests errors --harness xpcshell --message "Could not kill left-over process"`
- Got: no matches from either. The first is a placeholder that fx-tests synthesizes. The second is an INFO line, which the errors file does not keep.
- So there is no way to size a harness-level failure mode across tests.

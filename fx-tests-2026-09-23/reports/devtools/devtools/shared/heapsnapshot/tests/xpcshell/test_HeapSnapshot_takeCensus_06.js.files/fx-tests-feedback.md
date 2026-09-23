## Android no-output failures: the label hides the harness's reason, and cannot be searched

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_06.js`
- Expected: the failure mode of the 2 Android failures, e.g. `remotexpcshelltests.py | Failed to start process: … Could not kill left-over process` or `Could not read log file: … No such file or directory`. Those are the harness's own INFO lines, and they are in the job's resource-usage profile.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)` for both. The two have different mechanisms (low-memory kill at startup vs. launch refused), and the text points at logging, not at the launch.
- Then: `fx-tests failures --harness xpcshell --message "Failure details not recorded"` gives "No failure matched". So I could not ask "how many other tests hit this Android launch failure tree-wide". `fx-tests errors --harness xpcshell --message "Could not kill left-over process"` also matches nothing, because the errors file has no harness INFO lines.
- Workaround: loaded each job's resource-usage profile and searched the INFO markers for the test name.

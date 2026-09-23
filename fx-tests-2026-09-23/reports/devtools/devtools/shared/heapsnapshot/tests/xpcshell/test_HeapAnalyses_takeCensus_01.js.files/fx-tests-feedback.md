## "Failure details not recorded" hid a harness message that the resource profile has

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_takeCensus_01.js`
- Expected: the failure message for the Android run (task KS_byj8jRXez9yJIdsztvQ).
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`. The job's resource-usage profile has the cause as an INFO marker on the same test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Following `... will retry.` INFOs for the test would also find it.
- Workaround: load the resource profile and `thread markers --search <test file> --list`.
- Also: `fx-tests failures --message "Failure details not recorded"` then finds nothing, since the text is a placeholder.

## A test counted as failing in a job where 1152 of 1552 tests failed

- Command: `fx-tests test <path>` (Windows TIMEOUT from task PyUxuOpdQj2b3T3XyQOKpg).
- Expected: some sign that the job was broken as a whole (the Windows row is half of this test's failures).
- Got: `TIMEOUT Test exceeded time limit`, same weight as any other failure; only `fx-tests task` shows `1152 TIMEOUT`. The test never launched there: CreateProcess failed (`PermissionError: [WinError 5]`) and the harness's 45 s kill timer then reported a timeout.
- Suggestion: flag failures from jobs where most tests failed (e.g. "job-wide: 1152/1552 failed") in `test` output, or leave them out of the rate.

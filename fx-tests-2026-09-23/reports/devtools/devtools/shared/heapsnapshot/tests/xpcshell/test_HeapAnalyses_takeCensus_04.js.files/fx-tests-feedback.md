## Question: what was the failure message of an Android xpcshell failure
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_takeCensus_04.js`
- Expected: the failure mode of the Android run.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`.
- The job's resource-usage profile has it as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Picking up `Failed to start process:` lines for a test with no recorded message would have named it.

## Question: did this job collapse, rather than this test time out?
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Got: `1152 TIMEOUT` and my test's row `TIMEOUT — Test timed out`.
- What happened: 1149 tests started within 3.6 s and all "timed out" 45 s later at the same instant; the log ends with `Following exceptions were raised:` / `PermissionError: [WinError 5] Access is denied` from CreateProcess. A line flagging "N tests timed out within the same second" or surfacing the harness's end-of-run exceptions would have said "job collapse, not your test" without loading the profile.

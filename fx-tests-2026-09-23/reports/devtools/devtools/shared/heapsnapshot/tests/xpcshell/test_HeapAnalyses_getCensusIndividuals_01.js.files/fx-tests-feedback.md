## The failure message of an Android "Failure details not recorded" FAIL

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_getCensusIndividuals_01.js` and `fx-tests task ARmKO7HPQiKFSSUh1MuV-A --profiles`.
- Expected: the message that failed the test.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` and, in `task`, no message line at all. The message, `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`, is an INFO marker in that job's resource-usage profile.
- Workaround: downloaded the job's live_backing.log and grepped it, or searched the resource-usage profile for the test name.
- What would have answered it: for a FAIL with no failure line, show the job's `Failed to start process: <test>` INFO line (or any INFO/ERROR line naming the test between its TEST-START and TEST-FAIL) as the failure message.

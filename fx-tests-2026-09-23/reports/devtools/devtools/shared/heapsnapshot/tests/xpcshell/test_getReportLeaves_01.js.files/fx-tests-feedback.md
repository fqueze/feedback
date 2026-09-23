## Why did this Android run FAIL?

- Commands: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_getReportLeaves_01.js` shows the issue as "Failure details not recorded (likely Android or platform logging issue)". `fx-tests task IMKvf0T1THS6mvnmvpdqaA --profiles` lists the test as `FAIL — 1 failing execution of 2` with no message.
- Expected: the harness's reason for the failure.
- Got: nothing. The job's resource-usage profile has it as an INFO line: `remotexpcshelltests.py | Failed to start process: devtools/shared/heapsnapshot/tests/xpcshell/test_getReportLeaves_01.js | 0 | Could not kill left-over process`. Many heapsnapshot tests fail this way on Android.
- Workaround: load the resource-usage profile and search its markers for the test name.
- What would have answered it: for a FAIL with no failure line, show the job log's `Failed to start process: <test> | …` line (and `Failed wait for remote log`) as the failure message.

## Which slot did each test of this Android job run in, and which slot did the failing test have?

- Command: `fx-tests task IMKvf0T1THS6mvnmvpdqaA` gives outcomes per test, but not the slot (`XpcshellTestRunnerService$iN`) or pid of each run.
- Workaround: `profiler-cli thread markers --search 'launch_application,Launched Test App,left-over,will retry,Cleaning up profile,name:test' --list --limit 0 --json`, then a script (`slots.py` here). It pairs each `launch_application … $iN … _TEST_NAME` line with its test, and infers the slot of a test that never launched by elimination.
- What would have answered it: on Android jobs, the slot and pid of each execution in `fx-tests task`. Every "Could not kill left-over process" diagnosis needs them.

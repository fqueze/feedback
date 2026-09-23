## `fx-tests test` lumps two different Android harness failures under "Failure details not recorded"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_10.js`
- Expected: the failure modes told apart. The harness does print a line for each of these runs, in the job log and the resource-usage profile: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` (2 runs) and `<test> | Failed wait for remote log: … missing?` / `Could not read log file` (1 run).
- Got: `3x FAIL Failure details not recorded (likely Android or platform logging issue)`, and `fx-tests task <id> --profiles` shows only `FAIL — 1 failing execution of 2` for the test, without that line.
- Workaround: loaded each job's resource-usage profile and searched its markers for the test name.

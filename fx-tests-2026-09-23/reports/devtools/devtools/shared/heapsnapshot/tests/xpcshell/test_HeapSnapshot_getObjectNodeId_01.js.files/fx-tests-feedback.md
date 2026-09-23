## Android harness "Failed to start process" reported as "Failure details not recorded"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_getObjectNodeId_01.js` (and `fx-tests task RdP1lrpETimxPasuz1y_PQ --profiles`)
- Expected: the failure message for the Android run, which the job log does contain: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`; `task` lists the test as FAIL with no message and "No failing test named a per-test profile".
- Workaround: loaded the job's resource-usage profile and searched the INFO markers for the test name. Surfacing a `Failed to start process:` line for the test as its message would have answered it without a profile load, and would let `failures --message "Could not kill left-over process"` count this harness race tree-wide (it also hit test_system_startupprefs.js in the same job).

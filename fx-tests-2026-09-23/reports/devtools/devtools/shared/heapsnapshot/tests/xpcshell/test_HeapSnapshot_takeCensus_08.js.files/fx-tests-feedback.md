## Question: "what is the actual failure message of these Android FAILs?"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_08.js`
- Expected: the harness's own message for the failing execution.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)` for both Android runs. The resource-usage profile has the message as an INFO marker attached to the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Workaround: load each job's resource-usage profile and search for the test name. When no failure line was recorded, falling back to the `Failed to start process` / `Failed wait for remote log` INFO lines would separate the Android modes without loading a profile.

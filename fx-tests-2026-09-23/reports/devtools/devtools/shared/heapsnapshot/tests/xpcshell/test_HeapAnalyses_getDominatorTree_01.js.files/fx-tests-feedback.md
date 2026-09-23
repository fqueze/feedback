# fx-tests feedback (test_HeapAnalyses_getDominatorTree_01.js)

## Question: "what was the failure message for this Android FAIL"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_getDominatorTree_01.js` and `fx-tests task FmpkN-5vT7Ka-0EX6sEkWA --profiles`.
- Expected: the message behind the FAIL, or at least a pointer to the harness line that caused it.
- Got: "Failure details not recorded (likely Android or platform logging issue)" in `test`, and no message at all in `task`. The resource-usage profile has the answer as an INFO line: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Workaround: load the resource-usage profile and search for the test name. For Android xpcshell FAILs with an empty log, `fx-tests task` could surface the job's `Failed to start process: <test>` line; this mode affects many heapsnapshot tests (and dom/, toolkit/ ones) and is invisible from fx-tests today.

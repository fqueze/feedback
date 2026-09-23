## Question: "what was the failure message of this Android FAIL?" (test_census_diff_04.js)

- Commands: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_04.js` shows `FAIL Failure details not recorded (likely Android or platform logging issue)`; `fx-tests task ATzdBPseQliZQX1cvi1Zlw --profiles` shows only `FAIL — 1 failing execution of 2`.
- Expected: the harness's own line for the test, `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. It is in the job log and in the resource-usage profile as an INFO marker naming the test.
- Workaround: downloaded `live_backing.log` and grepped it. Five FAILs in this job had this same message; fx-tests shows none of them, so the one-bug-many-tests view (`fx-tests failures`) cannot group them either.

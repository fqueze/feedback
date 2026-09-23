## `task` shows no reason for an Android FAIL whose reason is in the job log

- Command: `fx-tests task H9xO7GAGSkeH3OfKxe3bMg --profiles` (and `fx-tests test <path>`, Issues: "Failure details not recorded (likely Android or platform logging issue)").
- Question: why did this Android xpcshell test FAIL with no failure line?
- Expected: the harness line explaining it, which is in the resource-usage profile as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: no message at all for the test in `task`, and "Failure details not recorded" in `test`.
- Workaround: load the resource-usage profile and `thread markers --search <test file>`. Surfacing any `Failed to start process: <test>` line as the failure message would answer it directly, and would group the same harness failure across the ~40 heapsnapshot tests that hit it.

## `test` Issues says TIMEOUT for a test that never launched

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_takeCensusDiff_01.js`
- Question: did the test time out, or did its job break?
- Got: `1x TIMEOUT Test exceeded time limit`, the same as a real timeout. Only `fx-tests task PyUxuOpdQj2b3T3XyQOKpg` shows 1,152 of 1,552 tests timing out in that job.
- Would help: flag a failure whose job had hundreds of simultaneous failures (e.g. "job-wide: 1,152 TIMEOUT in this job") in the `test` output, so it is not read as this test's timeout.

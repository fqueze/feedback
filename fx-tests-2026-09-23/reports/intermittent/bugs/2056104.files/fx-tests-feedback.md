## Failures logged as "unknown test url" are invisible to `test` and `task`

- Question: which test is behind bug 2056104 ("unknown test url | Checking for successful init login"), and how often does it fail per run?
- `fx-tests intermittent --bug 2056104` lists 34 task IDs, and "Tests named: 34x unknown test url".
- `fx-tests task TOSzChswR5ePiXk8zbKu1w --profiles` says "0 failing ... No test-level failure in this job", although the job is red with a `TEST-UNEXPECTED-FAIL` line.
- `fx-tests test toolkit/components/passwordmgr/test/mochitest/test_autofill_from_bfcache.html` reports 1 fail in 9,797 runs, while sheriffs annotated 34 failures of that test in 7 days (the harness records the test as PASS because the assertion comes from a helper window whose SimpleTest is not connected to the harness).
- Expected: `task` to list ERROR lines not attributed to a test, naming the test that was running (the last TEST-START) — the resource-usage profile's `output` markers already carry that attribution ("... — test_autofill_from_bfcache.html"). `intermittent --bug` could resolve "unknown test url" the same way.
- Workaround: streamed all 34 live_backing.log files and took the last TEST-START before the failure line with awk.

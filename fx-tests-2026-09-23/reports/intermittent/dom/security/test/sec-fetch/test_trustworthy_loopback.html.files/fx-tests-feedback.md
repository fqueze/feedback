## Failures reported as "logged result after SimpleTest.finish()" are invisible to `test` and `task`

- Question: how often does this test fail, and in which jobs?
- Command: `fx-tests test dom/security/test/sec-fetch/test_trustworthy_loopback.html` (also `--history`), then `fx-tests task eMfBMQQWQB2RFddm704oGw --profiles`.
- Expected: the 12 jobs sheriffs starred on bug 1805760 in the last 7 days (`fx-tests intermittent --bug 1805760` lists them) counted as failures of this test, with the task listing the TEST-UNEXPECTED-FAIL lines.
- Got: `test` says "Verdict: passing. 13,484 of 13,484 runs passed", 0 fail every day; `task` says "0 failing ... No test-level failure in this job" for all 7 starred tasks I checked. Yet the job's resource-usage profile holds three `FAIL` markers for this test ("logged result after SimpleTest.finish(): ..."), and the log has TEST-UNEXPECTED-FAIL. The test_end status stays PASS (TestRunner records these via `record()` / updateUI, not a failing test_end), so anything keyed on test_end status misses them.
- Workaround: took the task ids from `fx-tests intermittent --bug`, and read the FAIL markers in the resource-usage profile.
- Could have shown: count test_status FAIL / TEST-UNEXPECTED-FAIL lines under a PASS test_end as a failure (or at least a separate "unexpected subtest after finish" row), and list them in `task`.


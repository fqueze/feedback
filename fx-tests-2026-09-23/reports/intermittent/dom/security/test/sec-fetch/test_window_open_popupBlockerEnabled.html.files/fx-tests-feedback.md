## Failures "logged result after SimpleTest.finish()" are invisible to `test` and `task`

- Command: `fx-tests test dom/security/test/sec-fetch/test_window_open_popupBlockerEnabled.html` (and `--history`)
- Expected: the ~74 Android failures sheriffs starred on bug 1910959 in the same 21-day window (24 in the last 7 days per `fx-tests intermittent --bug 1910959`), with their configs.
- Got: `14,353 runs 14,353 pass (100.00%) 0 fail`, `Verdict: passing`. The failure is a subtest result (`TEST-UNEXPECTED-FAIL ... logged result after SimpleTest.finish(): ...`) recorded after the test ended, while the test's own status stays PASS/OK, so it is not counted.
- Same with `fx-tests task P_jpsKJUSM6eIEwax-p4WQ --profiles`: "631 tests ... 0 failing ... No test-level failure in this job", although the job's `mochitest-plain_errorsummary.log` has a `test_result` with `"status": "FAIL"` for this test and the group result is `ERROR`.
- Workaround: `fx-tests intermittent --bug`, then the Treeherder `failuresbybug` API for per-config counts, and the job logs.
- Could have shown: count a subtest FAIL (or a FAIL in the errorsummary) as a failing run of the test, or at least flag "passes, but has unexpected subtest failures" in the verdict and in `task`.

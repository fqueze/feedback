## Question: which tests are behind the occurrences of a harness-traceback bug?

- Command: `fx-tests intermittent --bug 2058686 --since 60`
- Expected: for each occurrence, the test the traceback belongs to, or at least the tests in flight when it was logged. The traceback is `PermissionError` from `runxpcshelltests.py testTimeout` and names no test.
- Got: "Tests named, per annotated job: (none …)" and three unrelated webtransport failures.
- Workaround: download all 75 `live_backing.log`s (606 MB) and script it: take the nearest preceding `INFO crashed process | … | <test>` or `<test> | timeout profile dump progressing` line, otherwise the TEST-START tests with no end before the traceback. All 74 turned out to be `toolkit/crashreporter/test/unit/` tests.

## Question: how often does this test time out? (when the harness never logged the TIMEOUT)

- Command: `fx-tests test toolkit/crashreporter/test/unit/test_crash_phc.js`
- Expected: the timeouts, or a warning that some runs have no result.
- Got: "0 timeout". The test timed out in 6 jobs of the window, but the harness raised before logging `test_end` (bug 2058686), so only the retry's PASS was recorded. The job does carry an ERROR log `test_start for <test> logged while in progress.` A run with a `test_start` and no `test_end` could be counted as "no result" rather than dropped.

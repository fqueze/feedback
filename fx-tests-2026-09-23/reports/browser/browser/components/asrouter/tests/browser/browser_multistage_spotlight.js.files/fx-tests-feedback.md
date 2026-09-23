## Question: which subtest of a test failed, in each of its failing jobs

- Command: `fx-tests task <taskId> --messages` (and `fx-tests test <path> --task-ids`)
- Expected: the subtest name next to each failure message. browser_feature_callout_in_chrome.js fails with the same `waitForMutationCondition - head.js:62 … timed out` in several subtests, and only one of them (`test_reused_callout_dispatches_with_current_browser`) leaks the stub that breaks the next tests.
- Got: messages only, no subtest, so telling which subtest failed in each job means loading a profile per job.
- Workaround: loaded per-test profiles and read the `Subtest` field of the TEST-UNEXPECTED-FAIL markers. The per-test profiles' TestStatus markers carry it; if the resource-usage profile's do too, `task --messages` could print it.

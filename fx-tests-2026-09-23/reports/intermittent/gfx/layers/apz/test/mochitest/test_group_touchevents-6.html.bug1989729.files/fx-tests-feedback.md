## Question: did this test also time out in this job?

- Command: `COLUMNS=250 fx-tests task Xtf-k7SdSAeNxYYk2PJACg --messages` (and `ReUtJFnMTdebz2NtpOr_Vw`)
- Expected: test_group_touchevents-6.html listed with its harness timeout as well as its two assertion failures.
- Got: `FAIL — 1 failing execution of 2` with only `contextmenu ... should not be delivered` and `touchstart ... should not be delivered`. The per-test profile shows the test then hung ~306 s and the harness reported `Test timed out` (a TEST-UNEXPECTED-FAIL with an empty message, from `TestRunner._checkForHangs`). That timeout, and a context menu left open, is what makes test_group_touchevents-7.html and test_group_touchevents.html time out right after it in the same jobs.
- Workaround: found it by listing Test markers in the profile.
- What would have answered it: an outcome of TIMEOUT (or "FAIL + TIMEOUT"), or the empty-message failure shown as the harness's `Test timed out`.

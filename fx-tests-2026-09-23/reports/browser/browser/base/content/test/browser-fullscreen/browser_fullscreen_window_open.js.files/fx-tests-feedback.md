## Question: which subtest timed out, per failing run
- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_fullscreen_window_open.js --task-ids --limit 0 --issue 3`
- Expected: the issue split, or at least annotated, by subtest. The log line is `TEST-FAIL | <path> | test_iframeWindowClickLinkOpenExistWindow - Test timed out`.
- Got: one issue `TIMEOUT Test exceeded time limit`, with no subtest. The errorsummary.log has no test_result line at all for runs whose harness retry passed, so it could not answer either.
- Workaround: streamed the live_backing.log of all 98 tasks through `rg "browser_fullscreen_window_open.js \| .*Test timed out"`. All 108 timeouts were the same subtest. It cost ~1 GB of downloads and a few minutes.

## Question: the test's failures across its rename
- Command: `fx-tests test <new path>` and `fx-tests test <old path>`, then adding the counts by hand.
- Expected: a note that the test was renamed (bug 2069131 moved it on 2026-09-11), or merged history. Both paths have 21-day windows that each go empty on one side of the date.
- Got: two separate reports. The per-config rates had to be summed by hand.

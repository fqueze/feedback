# fx-tests feedback

## `test` has no wpt data
- Command: `fx-tests test testing/web-platform/tests/screen-capture/getdisplaymedia-framerate.https.html` (also `/screen-capture/...` with `--harness wpt`)
- Expected: per-config rates and history for a web-platform-test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..."; `--harness` only accepts xpcshell|mochitest.
- Workaround: Treeherder jobs API for every `test-macosx1500-aarch64-shippable/opt-web-platform-tests-N` job since Sep 13, then `wpt_errorsummary.log` (first line, test_groups) to find the chunk and `live_backing.log` / `wptreport.json` to read the subtest status. About 30 minutes and ~450 artifact fetches.
- Question it could have answered: "on config X, what was this subtest's status and message in each run over the window?"

## `task` on a wpt job lists expected statuses as failures, and misses the real one
- Command: `fx-tests task D239AvtmRj633KdLsPA1rA --profiles`
- Expected: the unexpected results of the job (here one TEST-UNEXPECTED-PASS subtest in /screen-capture/getdisplaymedia-framerate.https.html).
- Got: "25 failing", all tests whose harness status is ERROR/TIMEOUT (bluetooth, webxr, protocol-handler...), which match their expectations; the UNEXPECTED-PASS test is not listed.
- Workaround: grep `TEST-UNEXPECTED` in the task log.

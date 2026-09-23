# fx-tests feedback (parallel-capture-requests.https.html, WPT)

## `fx-tests test` has no WPT data

- Command: `fx-tests test testing/web-platform/tests/mediacapture-streams/parallel-capture-requests.https.html` (and `--history`)
- Expected: rates per config, history.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. The message does not say that WPT is not covered at all, so I first suspected a wrong path.
- Workaround: `fx-tests intermittent --bug N --json` (occurrenceRows carry machineName/taskId), plus the Treeherder jobs API and each job's `wpt_errorsummary.log` to count passes. That took a hand-written script and a long, slow Treeherder crawl.

## `fx-tests task` misses unexpected WPT subtest failures and lists expected ERRORs

- Command: `fx-tests task IHTiO7puRCyVlQKwsEf28A --profiles`
- Expected: `/mediacapture-streams/parallel-capture-requests.https.html` among the failures. Its two subtests are TEST-UNEXPECTED-FAIL, and they are why the job is orange.
- Got: "FAILED (9) — every test this job recorded a failure for". The list names 9 tests whose outcome is ERROR, for example `/mediacapture-streams/MediaStreamTrack-transfer-video.https.html`, whose TEST-ERROR is expected (no UNEXPECTED in the log). It does not include the test with the unexpected subtest failures, whose harness status is OK.
- Workaround: read `wpt_errorsummary.log` / `wptreport.json` directly.

## Question: "which hosts is this failure on, and do those hosts ever pass it?"

- `fx-tests intermittent --bug N` prints no machine name in its occurrence table; only `--json` has `machineName`. For an environment-shaped intermittent, a per-host count (fail count per worker) would have answered this directly.

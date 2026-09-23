# fx-tests feedback (overscroll-behavior.html.bug2070403)

## Question: how often does this web-platform test fail, per config, and since when?

- Command: `fx-tests test testing/web-platform/tests/css/css-overscroll-behavior/overscroll-behavior.html` (also `--history`)
- Expected: per-config pass/fail rates for a WPT, like for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". WPT is not covered at all.
- Workaround: Treeherder `/api/jobs/?job_type_name=...` to list the backlog jobs, then a script
  downloading each job's `wptreport.json` and extracting this test's subtest statuses and
  expectations (126 jobs). A `fx-tests test` for WPT paths could show, per config, the subtest
  status vs expected counts (PASS/TIMEOUT for one subtest here), which is the whole answer.

## Question: which failure message did sheriffs star on this bug?

- Command: `fx-tests intermittent --bug 2070403`
- Expected: the failure lines of this bug's annotated jobs (all 7 are
  `TEST-UNEXPECTED-PASS | /css/css-overscroll-behavior/overscroll-behavior.html | overscroll-behavior-y: chain allows scroll propagation on y axis - expected TIMEOUT`).
- Got: "Failure messages, per annotated job: 2x PeerConnection passthrough MediaRecorder ..."
  and "Tests named: 2x /mediacapture-record/passthrough/MediaRecorder-passthrough.https.html",
  i.e. another test's failures from the same jobs; this bug's own message is not listed.
- Workaround: downloaded all 7 job logs and grepped them.

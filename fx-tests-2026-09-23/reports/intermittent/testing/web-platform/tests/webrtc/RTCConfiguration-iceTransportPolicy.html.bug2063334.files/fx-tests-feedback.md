## Question: how often does this WPT test fail, and on which configs
- Command: `fx-tests test testing/web-platform/tests/webrtc/RTCConfiguration-iceTransportPolicy.html`
- Expected: rates per config, like for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` -- WPT is not covered at all.
- Workaround: Treeherder `similar_jobs` for each job type (6 of them), then one `wpt_errorsummary.log` per task (~1100 fetches) and a script to classify. Cost most of the investigation time.

## Question: which tests failed in a job that hit max-run-time
- Command: `fx-tests task TGAz2EQ4Rt-rkTjnC0xfuw --profiles` (and 5 other debug tasks)
- Expected: the job's per-test outcomes.
- Got: "killed for exceeding its maximum duration ... does not read that format".
- Workaround: the task still uploaded `public/test_info/wpt_errorsummary.log` and the live log; parsed those. `task` could fall back to the errorsummary for WPT.

## Question: which workers did the annotated failures run on
- Command: `fx-tests intermittent --bug 2063334`
- Got: occurrences with tree/platform/task id, but no machine name.
- Workaround: Treeherder jobs API per task. All 10 turned out to be on the same 28-machine subset; a worker column would have shown that at once.

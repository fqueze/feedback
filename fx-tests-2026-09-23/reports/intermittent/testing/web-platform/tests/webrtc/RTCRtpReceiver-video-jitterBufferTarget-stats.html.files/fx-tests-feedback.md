## Question: "how often does this WPT test fail, on which configs, since when?"

- Command: `fx-tests test testing/web-platform/tests/webrtc/RTCRtpReceiver-video-jitterBufferTarget-stats.html` (also `--history`)
- Expected: rates per config and history, as for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` — WPT is not covered at all (`--harness` only accepts xpcshell|mochitest).
- Workaround: script over Treeherder `similar_jobs` for each job type, then fetch every task's `public/test_info/wpt_errorsummary.log` and look for the test (~1000 fetches). Showed the real rate (~12% on mac debug) is far above what sheriffs starred (9 annotations, 6 of them unexpected PASS).
- What it could show: the same per-config table from the WPT errorsummary / wptreport data. Note that for WPT a subtest FAIL leaves the test status `OK`, so a per-test status count would miss it: subtest results matter.

## Question: "which tests failed in this WPT job?"

- Command: `fx-tests task FHzFGfukRZC9V0MHgJ5RIQ --profiles`
- Expected: the job's failures, including `/webrtc/RTCRtpReceiver-video-jitterBufferTarget-stats.html` (subtest FAIL, starred on bug 1947377).
- Got: `FAILED (1)` listing only `/webrtc-extensions/RTCRtpCorruptionDetection-headerExtensionControl.html`; the unexpected subtest FAIL of this test (test status OK) is not listed.
- Workaround: read `public/test_info/wpt_errorsummary.log`.

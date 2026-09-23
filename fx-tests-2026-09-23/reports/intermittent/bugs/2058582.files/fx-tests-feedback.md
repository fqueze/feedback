## gtest jobs: `fx-tests test` has no data, and `fx-tests task` reports "0 tests"

- Command: `fx-tests test WebRtcIceGatherTest.VerifyTestStunServer` and `fx-tests test dom/media/webrtc/transport/test/ice_unittest.cpp`
- Expected: rates per config, like for xpcshell/mochitest. Got: "No test path in the xpcshell and mochitest 21-day data contains ...". gtest is not covered at all.
- Command: `fx-tests task Q8EqXfevT6msU-st3SYsFg --profiles` (a gtest-1proc job)
- Expected: the job's tests with outcomes. Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", while the resource-usage profile it links has 15598 `test` markers and 196 `FAIL` markers, one per gtest.
- Workaround: read the resource-usage profile with profiler-cli and the live_backing.log directly.

## Skipped gtests are counted as failures

- Same command. The warning lists `FAIL FontFallbackTest.ListFontsUsedForString_FallbackOrder`, `..._MultipleFontsWithEmoji`, `..._VisibilityWithMultipleFonts` among the 83 failing markers.
- The log says `TEST-PASS | FontFallbackTest.ListFontsUsedForString_FallbackOrder | Need at least 2 fonts for fallback order test`: a GTEST_SKIP, not a failure. Either the profile's markers or fx-tests misclassify it.

## Question: "which workers do this bug's failures land on, against all runs of the job there?"

- This bug's failures are 287 of 749 runs on macmini-m4-153, -174..-183, -197..-215, and 5 of 4004 runs on the rest of the pool. That was the whole diagnosis, and no fx-tests command shows it.
- `fx-tests intermittent --bug 2058582 --limit 0` lists task ids but no worker, and no denominator per worker.
- Got it with a script over Treeherder's `/api/failuresbybug/` (has `machine_name`) and `/api/project/<repo>/jobs/?job_type_name=...` (all runs, with `machine_name`).
- What would have answered it: a per-worker (or per-worker-range) failure/run table in `fx-tests intermittent --bug`, flagging a concentration.

## hg.mozilla.org raw-file returns 406

- `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/ac88765add9fabc9f657353b9fde8b9428f5fb48/dom/media/webrtc/transport/test/ice_unittest.cpp` -> HTTP 406, empty body (also with a browser User-Agent). Read the files at local `origin/main` (f66648681c87) instead.

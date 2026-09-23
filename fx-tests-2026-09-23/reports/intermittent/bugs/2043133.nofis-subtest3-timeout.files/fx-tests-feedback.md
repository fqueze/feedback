## Question: "where did each TIMEOUT of this test stop?"

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --config nofis --task-ids --issue 2 --limit 0` (60 TIMEOUTs).
- Question: for each timed-out run, what was the last test output (subtest and assertion)? That is what tells one timeout mode from another in a test with several.
- Got: task IDs only. On Android the live log has no full-log block for a timeout (only `TEST-ERROR ... Timed out and was forcibly killed`), and the per-run parent pid is `0` for many runs ("| 0 | Launched Test App"). I had to download 59 logcats (about 20 MB gzipped each), find each timed-out run's parent pid by matching `XpcshellTestRunner: Got extras arg24=const _TEST_NAME = ...` against the host's launch time, and take its last `test_status` JSON line (`analyze.py`).
- Could show: a "last output before the timeout" column per occurrence, read from the full-log block on desktop and from the logcat's JSON lines on Android. Grouping the 60 by it would have given the 55 / 2 / 2 split at once.

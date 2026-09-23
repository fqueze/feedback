## A mochitest timeout is shown only as "No checks actually run", and counted as FAIL

- Command: `fx-tests test dom/events/test/pointerevents/test_synthesized_touch.html` and `fx-tests task GE0XzmcMRSSo-MB0_9L5-w --messages`.
- Expected: the failure recorded as a timeout, or at least its first message, `Test timed out.` (the profile's first `TEST-UNEXPECTED-FAIL` for the test has subtest `Test timed out.` from `TestRunner._checkForHangs`, 5 min after the test started).
- Got: `fx-tests test` lists all 341 failures under `FAIL  Failure details not recorded (likely Android or platform logging issue)`, with `0 timeout`. `fx-tests task --messages` shows only `[SimpleTest.finish()] No checks actually run.`, which reads like a test that finished without checking anything, not like a hang.
- Workaround: load the per-test profile and read the test's `TEST-UNEXPECTED-FAIL` markers. The question "did it time out?" took a profile load to answer.

## Question: which other tests fail in the same jobs as this one, and how often

- Commands: `fx-tests test <path> --task-ids --limit 0` for `test_synthesized_touch.html`, `test_remove_frame_when_got_pointer_capture.html`, `test_pointermove_isPrimary_subsequent_pens.html` and `test_pointerevent_altitude_azimuth_angle.html`, then `rg`/`sort`/`comm` over the task IDs.
- What I needed: "in the 341 jobs where this test failed, which other tests failed too, and in how many jobs". Here it gives isPrimary's 112 ⊂ remove_frame's 323 ⊂ this test's 341, and the altitude test's 15 non-xorig failures in none of them. That nesting was the evidence for an order-dependent victim.
- What the output could show: a "failed in the same job" table in `fx-tests test <path>` (`--co-failures`?). It could list the co-failing tests with counts, and whether they ran before or after this test in the job.

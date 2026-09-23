## A mochitest-plain timeout is counted as a FAIL with "Failure details not recorded"

- Command: `fx-tests test dom/serializers/tests/mochitest/test_bug116083.html` and `fx-tests task cheMJ1zySBGS1h2y6-nIoA --messages`.
- Expected: the 6 macOS failures counted as timeouts, with "Test timed out." as their message.
- Got: `0 timeout`, 6 FAIL under "Failure details not recorded (likely Android or platform logging issue)", and in `task`, "[SimpleTest.finish()] No checks actually run." as the message. The per-test profile shows the first failure was `TEST-UNEXPECTED-FAIL` with `Subtest: Test timed out.` and an empty `Message` (from `TestRunner._checkForHangs`); "No checks actually run" is its fallout from `killTest`.
- Workaround: load the profile and read the Test markers. The same misreport applies to `test_copypaste_disabled.html` and `test_canvas_focusring.html` in the same jobs.

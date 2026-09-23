## A mochitest-chrome timeout is reported as "Failure details not recorded", with 0 timeouts

- Command: `fx-tests test widget/tests/test_bug428405.xhtml` (and `--task-ids`, `fx-tests task PCx7JJ66SpCJkNveIZ-zmA --profiles`)
- Expected: the 23 failures counted as timeouts, the issue as `Test timed out.`
- Got: `0 timeout`, Issues `23x FAIL Failure details not recorded (likely Android or platform logging issue)`;
  `fx-tests task` shows `[SimpleTest.finish()] No checks actually run.` as the test's message, which is
  the fallout of `killTest`, not the failure.
- In the per-test profile the first `TEST-UNEXPECTED-FAIL` (from `TestRunner._checkForHangs`) has an
  empty Message and `Subtest: Test timed out.`, so the text is in the subtest field, not the message.
- Workaround: loaded the per-test profile and read the TestStatus markers' Subtest field.

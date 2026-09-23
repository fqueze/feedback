## `fx-tests test` Issues section hides a failure message that `fx-tests task` knows

- Command: `fx-tests test dom/canvas/test/test_canvas_focusring.html`
- Expected: under Issues, the failure message, e.g. `Test timed out.` / `[SimpleTest.finish()] No checks actually run. ...`
- Got: `6x  FAIL    Failure details not recorded (likely Android or platform logging issue)`. All 6 are macOS mochitest-plain runs, and `fx-tests task <id> --profiles` shows the message for each of them.
- Cost: I had to run `fx-tests task` on each failing task just to learn the failure mode, which is what the Issues section is for.
- Workaround: `fx-tests intermittent --bug <N>` (sheriffs' messages) or `fx-tests task <taskId>`.

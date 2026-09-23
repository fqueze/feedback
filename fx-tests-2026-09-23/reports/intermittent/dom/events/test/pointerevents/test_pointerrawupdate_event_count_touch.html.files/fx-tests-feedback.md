## `fx-tests test` gives no failure message for a Windows mochitest-plain timeout

- Command: `fx-tests test dom/events/test/pointerevents/test_pointerrawupdate_event_count_touch.html`
- Expected: under Issues, the failure message (`Test timed out.` / `[SimpleTest.finish()] No checks actually run.`) with its count.
- Got: `480x FAIL Failure details not recorded (likely Android or platform logging issue)` — all failures are on Windows desktop, not Android, and `fx-tests task <id>` does show the message for the same runs.
- Workaround: `fx-tests intermittent --bug 1985017` (sheriff-annotated jobs only) and `fx-tests task <id> --profiles` per job.

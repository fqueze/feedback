## Question: what is the failure message of an Android-only intermittent?

- Command: `fx-tests test dom/media/test/test_cloneElementVisually_mediastream.html`
- Expected: the failure modes under Issues ("Test timed out", "No checks actually run").
- Got: `31x FAIL Failure details not recorded (likely Android or platform logging issue)` — yet `fx-tests task <id>` and `fx-tests intermittent --bug 1781561` both show the real message for the same jobs.
- Workaround: ran `fx-tests task` on individual tasks.

## Question: how many runs and failures on emulator vs hardware Android?

- Command: `fx-tests test <path> --coverage --limit 0`
- Expected: a per-platform-family subtotal (e.g. android-em vs android-hw), since the failing-config table only lists configs that failed.
- Got: one row per config; needed awk to sum 2027 emulator runs / 31 fails vs 1890 hw-a55 runs / 0 fails.

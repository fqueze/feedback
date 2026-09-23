## Question: which failure modes does this test have, and how many runs each?

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings.js` (also `--task-ids --full-messages`, `--json`)
- Expected: the Issues block to list the real failures: `NotFoundError: Could not get children of .../testLastBackupInfo` (87 jobs) and `Node is not accessible via accessibility API: id: main-button` (65 jobs).
- Got: every one of the 189 failing runs is attributed to `handleEvent() was unable to perform a11y checks on hidden node: id: main-button`, which is a `TEST-KNOWN-FAIL` (a `SimpleTest.todo` from AccessibilityUtils) logged by every run, passing or not. The real failure messages appear nowhere in `fx-tests test`. With `--config`, some configs instead show `Failure details not recorded (likely Android or platform logging issue)`, and the per-config Issues counts exceed the config's fail count (109x for linux2404-64-shippable a11y-checks, which has 63 fails).
- Workaround: looped `fx-tests task <id> --messages --full-messages` over all 150 task IDs and classified the output with a script (~3 min).
- Could have shown: skip TEST-KNOWN-FAIL / todo messages when picking a run's failure message; and a per-message breakdown (jobs and runs per failure message, per config).

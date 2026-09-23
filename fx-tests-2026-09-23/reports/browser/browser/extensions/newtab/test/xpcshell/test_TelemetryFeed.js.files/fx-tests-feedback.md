## Failures without a per-test profile (question: "which profile covers this test's failure")

- Command: `fx-tests task FN28SmhaRQOc78Qf-Okjrw --profiles` (and 5 other tasks)
- Expected: a profile line for `test_TelemetryFeed.js`, or an explicit "no per-test profile was uploaded for this failure".
- Got: the row for this test has no profile line, while the other rows do; nothing says one is missing.
- Workaround: listing the task's artifacts with `curl` to confirm no `profile_test_TelemetryFeed.js.json` exists, and grepping the log for "profile uploaded".
- What the output could have shown: "no profile uploaded" on the row (here because the test stops the profiler itself, so the harness's scheduled timeout dump never happens).

## `--bugs` prints nothing when the only bug names an old path

- Command: `fx-tests test browser/extensions/newtab/test/xpcshell/test_TelemetryFeed.js --bugs`
- Expected: a Bugs section, or "no bugs found".
- Got: exactly the output without `--bugs`; no Bugs section and no "none" line.
- Workaround: Bugzilla quicksearch for the file name found bug 1875918, "Intermittent browser/components/newtab/test/xpcshell/test_TelemetryFeed.js | single tracking bug" (old path).
- What the output could have shown: "no bug names this path", plus bugs naming the same file name under another directory.

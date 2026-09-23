## Harness-level failures (no test path) are invisible to `failures`

- Question: how often does "RemoteProcessMonitor | application timed out after 370 seconds with no output" happen, starred or not, and on which configs?
- Command: `COLUMNS=250 fx-tests failures --harness mochitest --message "timed out after 370 seconds" --tests`
  (also `--message "incomplete after application is no longer top"`)
- Expected: rows for the RemoteProcessMonitor failures that `fx-tests intermittent --bug 1987687` lists (10 in 7 days).
- Got: "No failure matched. Searched 21,956 tests in mochitest-issues.json".
  The failure has no test path, so it seems to be dropped upstream. Nothing said so; it read as "this never happens".
- Workaround: Treeherder `https://treeherder.mozilla.org/api/failuresbybug/?startday=...&endday=...&tree=all&bug=1987687`
  (starred occurrences only).
- Could show: a note that harness-level failures without a test path are not in this file.

## `intermittent --bug` only covers 7 days

- Question: what mix of failure modes has this bug had over months? 8 of the 10 annotations in the 7-day window came from one push (a regression that was backed out), which hid the bug's usual mode.
- Command: `fx-tests intermittent --bug 1987687`
- Got: 7 days only (2026-09-16 to 09-22). No `--since`/`--days` to go further back.
- Workaround: Treeherder `failuresbybug` API over 2026-06-01..09-22 (144 rows), then a script to bucket them by message and platform.
- Could show: a longer window, and a warning when most occurrences share one revision ("8 of 10 on e589ebeb6bc0").

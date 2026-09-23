## Question: which jobs failed with a harness-level message not attributed to any test ("RemoteProcessMonitor | application timed out after 370 seconds with no output", "RemoteProcessMonitor | exception reading log")

- Command: `fx-tests failures --harness mochitest --message "timed out after 370 seconds" --limit 0` (and `--message "exception reading log"`)
- Expected: the jobs (or at least counts per day/config) where the Android harness logged these lines, sheriff-annotated or not.
- Got: "No failure matched. Searched 21,956 tests in mochitest-issues.json" — messages logged against `RemoteProcessMonitor` (no test in flight) are not in the per-test data at all.
- Workaround: `fx-tests intermittent --bug 2021271 --since 21 --limit 0` for the sheriff-starred jobs only, then downloading each job's live_backing.log and logcat by hand. That gives no rate and no denominator (how many Android mochitest jobs ran without hitting it).
- What could answer it: a harness-message view (message, job name, task id, day) for `log`-level ERROR records with no test, like the CRASH-on-manifest markers `fx-tests task` already warns about.

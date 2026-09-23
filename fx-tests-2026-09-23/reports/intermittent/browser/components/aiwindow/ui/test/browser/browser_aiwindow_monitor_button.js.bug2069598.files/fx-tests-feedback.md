## `fx-tests task --profiles` omits the hang profile of an "application timed out" test

- Command: `fx-tests task NILiRZR-QT6agXl6m2WsaA --profiles` (and `BOpdbx_DS8a1_yVFVGVw-w`)
- Expected: for `browser_aiwindow_monitor_button.js  TIMEOUT — application timed out after 740.0 seconds with no output`, the profile the harness captured of the hung browser (`public/test_info/profile_0_1751.json`, named in the log as "Symbolicating profile_0_1751.json" after "Attempting to start the profiler to help with diagnosing the hang").
- Got: no profile line under that test, and for BOpdbx "No failing test named a per-test profile in this job", although the task has `profile_0_2011.json` and `profile_0_7618.json`.
- Workaround: listed the task's artifacts with curl and matched the pid from the resource profile's `Application pid:` / `Sending SIGUSR1 to pid` markers.

## `fx-tests test` does not count test-verify runs, and `try --test` gives no task ids

- Question: "which test-verify jobs of push 04c12a7e3f78 failed this test, with their task ids?"
- `fx-tests test <path>` has no test-verify rows (the 09-21 macOS failures are absent from its 21-day window). `fx-tests try 04c12a7e3f78 --project autoland --test <path> --all-jobs --task-ids` gave the per-config table (both macOS test-verify jobs failed) but printed no task ids. Found the debug one through `fx-tests intermittent --bug 2069600 --json`.

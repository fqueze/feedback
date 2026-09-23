## `fx-tests task` does not list the hang profiles of an application timeout

- Command: `fx-tests task DkKZUi2iR2ytWDgVcFyh5Q --profiles` (test-macosx1500-aarch64-vms/opt-mochitest-remote, every browser launch hit `automation.py | application timed out after 370.0 seconds with no output`).
- Expected: the profiles of the failure. The task uploaded nine full-session Firefox profiles, `public/test_info/profile_0_<pid>.json`, one per hung launch. The harness saves them when it kills the hung browser.
- Got: `1 tests, 1 executions, 0 failing`, `Outcomes: 1 SKIP`, only the resource-usage profile, and "No test-level failure in this job ... Read the log".
- Workaround: list the task artifacts with the Taskcluster API and load `profile_0_<pid>.json` by hand. The first launch's pid is in the log's `Application pid:` line.
- Question it could have answered: "which profiles show this job's hung launches". A `profile_0_<pid>.json` artifact could be listed with the log line (`Application pid: N`, then the timeout) that it belongs to.

## Harness timeouts do not show in any per-test rate

- Command: `fx-tests test remote/shared/listeners/test/browser/browser_ChromeWindowListener.js --history --config macosx1500-aarch64-vms`.
- Expected: some sign of 40 failed jobs on this config between 2026-09-16 and 09-21.
- Got: `257 of 257 runs passed`. The only trace is fewer passes per day (13, 4, 7, 2, 3 against 15-24 before). A job that never started a test adds nothing to the counts.
- Workaround: Treeherder's jobs API per push, then read every job's log (`collect.py`).
- Question it could have answered: "how many jobs of this config failed before running any test". A per-config count of jobs with no test results would answer it.

## `intermittent --bug` truncates the job name and platform in the occurrence rows

- Command: `fx-tests intermittent --bug 2022292 --limit 0`.
- Got: `macosx1500-…  opt    mochitest-r…`. The job name and platform are cut even with `--limit 0`, so you cannot tell `-vms` from hardware, or `-shippable` from not.
- Workaround: `--json`, then `occurrenceRows[].platform` and `.testSuite`.
- Question: "which config and chunk each annotated job is". Printing the full `platform` and `testSuite` (or honouring COLUMNS) would answer it.

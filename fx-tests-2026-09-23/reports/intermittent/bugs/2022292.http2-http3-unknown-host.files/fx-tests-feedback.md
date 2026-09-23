## `intermittent --config` does not match the job name it prints

- Command: `fx-tests intermittent --bug 2022292 --since 90 --config http --json`.
- Expected: the bug's occurrences in `mochitest-plain-http2-*`, `mochitest-plain-http3-*` and `mochitest-devtools-chrome-http3-*` jobs. `--help` says `--config` takes "job-name substrings".
- Got: `fx-tests: bug 2022292 has 571 annotations on trunk, but none match the filter`, exit 1. Without `--config`, 62 rows have `http` in `testSuite`.
- Workaround: no `--config`, `--json`, then filter `occurrenceRows[].testSuite` in Python.
- Question it could have answered: "this bug's occurrences on one job family, with their task ids". `--config` could match `testSuite` (or the full job name), or `--help` could say which field it matches.

## `task --profiles` does not list the hang profiles of an application timeout (repeat of `2022292.files/fx-tests-feedback.md`)

- Command: `fx-tests task FtbYm8lsTRO2C1qo8jzh4g --profiles`.
- Got: only the resource-usage profile, and "No test-level failure in this job ... Read the log". The task also uploaded `public/test_info/profile_0_24255.json`, the full-session profile of the hung browser, which is where the diagnosis was.
- Workaround: the Taskcluster artifacts API, and the `Application pid:` line before the timeout.

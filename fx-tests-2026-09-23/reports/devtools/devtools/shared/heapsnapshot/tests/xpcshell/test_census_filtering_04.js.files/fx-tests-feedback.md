## Why did this Android run fail? (test_census_filtering_04.js)

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_filtering_04.js`
- Expected: the harness line that explains the failure.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`, and `fx-tests task TktpQjzIT0mAKOsOT7x4WA` printed no message for the row either.
- The resource-usage profile has the answer as an INFO marker attributed to the test:
  `remotexpcshelltests.py | Failed to start process: devtools/shared/heapsnapshot/tests/xpcshell/test_census_filtering_04.js | 0 | Could not kill left-over process`.
  When a failing test has no FAIL/ERROR message, its `Failed to start process` (or other remotexpcshelltests.py) INFO lines could be shown instead.
- Workaround: loaded the resource-usage profile and searched the markers for the test name.

## Was this job's failure specific to the test? (test_census_filtering_04.js)

- Command: `fx-tests test <path> --task-ids`
- Expected: some sign that the Windows TIMEOUT came from a job where 1,154 tests failed (1,152 TIMEOUTs).
- Got: a plain `TIMEOUT Test exceeded time limit` issue with the task ID. You only find out with `fx-tests task <id>`.
- Suggestion: next to each task ID in `test --task-ids`, show how many tests failed in that job ("1 of 1154 failures in this job"), so mass failures are visible at once.

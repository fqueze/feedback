## Question: "this one test's outcome in a job with 1,000+ failures"
- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to see the row of the test I am investigating.
- Got: the list truncated to 20 of 1,061 failures, so the test's row was not shown.
- Workaround: `--limit 0` into a file, then grep for the test path.
- Could have shown: a `--test <path>` filter, or always print the row of the test passed via `fx-tests test ... --task-ids`.

## Question: "why did the Android run FAIL with no details"
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_pluralForm-japanese.js`
- Expected: some hint of the failure cause.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`. The cause was in harness INFO/WARNING lines naming the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` in one job, and `Failed wait for remote log` / `Could not read log file` in the other.
- Workaround: loading the resource-usage profile and searching the markers for the test name.
- Could have shown: for a FAIL with no TEST-UNEXPECTED line, the harness INFO/WARNING lines that name the test.

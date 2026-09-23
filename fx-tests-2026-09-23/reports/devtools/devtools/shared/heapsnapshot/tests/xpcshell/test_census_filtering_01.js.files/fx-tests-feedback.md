## Android harness-level failure shows as "Failure details not recorded"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_filtering_01.js` and `fx-tests task blyH85BaSbyjFRb1PCQ7-Q --profiles`
- Expected: the failure message of the run, which the harness did log: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` (an INFO line, present as a TestStatus marker in the job's resource-usage profile).
- Got: Issue "Failure details not recorded (likely Android or platform logging issue)", and `fx-tests task` lists the test with no message. The question "which tests / jobs hit `Could not kill left-over process`" has no answer in fx-tests: tree-wide it lands in the `(no message recorded)` bucket of `fx-tests failures`.
- Workaround: loaded the resource-usage profile and searched `thread markers --search "left-over,Failed to start"`.
- Suggestion: when a test has no TEST-UNEXPECTED line, fall back to the harness's `Failed to start process: …` / `… will retry` INFO line for that test.

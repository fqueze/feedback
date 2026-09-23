## Question: why did this Android xpcshell test FAIL, when the failure line is "Failure details not recorded"?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_07.js`
- Expected: the failure mode to name the harness's own reason. It is in the job log (and in the resource-usage profile as an INFO marker): `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `4x FAIL Failure details not recorded (likely Android or platform logging issue)`, for all 4 Android failures. `fx-tests task <id>` also shows a bare `FAIL`.
- Workaround: load each job's resource-usage profile and search markers for the test name. The harness's `Failed to start process: ...` INFO line, logged inside the test's TEST-START/TEST-END, could be used as the failure message when there is no other.

## Question: how many tests, tree-wide, fail because the Android harness logged "Could not kill left-over process"?

- Command: `fx-tests failures --harness xpcshell --path devtools/shared/heapsnapshot --tests --limit 0`, and `fx-tests errors --harness xpcshell --message "Could not kill left-over process"`
- Expected: a row for that message, with its tests.
- Got: every such failure sits in `(no message recorded)` (97 failures in 57 tests in this directory alone), and `errors` has no match because the line is a harness INFO line, not an error marker.
- Workaround: count it by hand in each job's resource-usage profile (4, 4, 2 and 1 tests in the four jobs I read). Using the harness's `Failed to start process: …` / `Could not read log file: …` INFO line as the message of a FAIL that has none would make this a one-command answer.

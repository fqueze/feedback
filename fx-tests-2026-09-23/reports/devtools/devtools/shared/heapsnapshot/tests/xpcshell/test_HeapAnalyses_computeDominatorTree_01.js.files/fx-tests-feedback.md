## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_computeDominatorTree_01.js --bugs`
- Expected: a "Bugs: none found" line (or the list).
- Got: the same output as without `--bugs`, no bugs section at all, so I could not tell "no bug" from "flag ignored".
- Workaround: a Bugzilla REST summary search.

## Question: "what did the harness actually log for this FAIL on Android?"

- Command: `fx-tests test <path>` / `fx-tests task <taskId> --profiles`
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`, and "No failing test named a per-test profile in this job".
- The job's resource-usage profile has the answer as a TestStatus INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. `fx-tests task` could show the test's `Failed to start process` / `Failed wait for remote log` lines as its failure message. That would also group these Android harness failures into one Issue across the heapsnapshot tests, instead of each looking like an unknown FAIL.

## Question: "which Android xpcshell slot did each test run in, and which process names did each pid carry?"

- Needed: `profiler-cli thread markers --search launch_application --list --limit 0 --json` and `--search get_process_list …`, then a Python script to pull `XpcshellTestRunnerService$iN` + `_TEST_NAME` out of each launch line and the `…:xpcshellN` entries out of each `ps` list. After that, working out slot occupancy by elimination.
- What would have answered it: a `fx-tests task <taskId> --slots` (Android xpcshell) view listing each test with its slot, launch time and pid. It could also list the pids whose `…:xpcshellN` name changes, which is what diagnoses every "Could not kill left-over process" failure. Sibling reports in this directory needed the same script.

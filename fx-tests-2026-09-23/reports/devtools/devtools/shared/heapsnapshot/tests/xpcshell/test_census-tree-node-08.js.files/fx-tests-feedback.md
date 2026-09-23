## Android FAIL with "Failure details not recorded" hides a harness message that is in the job's log

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-08.js` (and `fx-tests task MnqH31VNQl-FNdGyAIIelQ --profiles`)
- Expected: the failure mode named by the line that caused it.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` for all 3 Android FAILs, and the task view listed the test with no message. The resource-usage profile of each job has the cause as an INFO line: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Workaround: load each job's resource-usage profile and search its markers for the test name.
- Could have shown: for a FAIL with no failure line, the `remotexpcshelltests.py | Failed to start process: …` / `Failed wait for remote log` / `Could not read log file` INFO line that names the test, as the Issues entry. That alone groups these failures across tests into one harness failure mode.

## Question: which Android slot (`XpcshellTestRunnerService$iN`) did a test run in?

- Needed to tie a `Could not kill left-over process` failure to the process names in the `get_process_list` DEBUG lines.
- Command: `profiler-cli thread markers --search "launch_application,Launched Test App,Cleaning up,will retry" --list --limit 0 --json`, then a script. The slot has to be worked out by elimination, because a test that failed before launch has no `$iN` line.
- Could have shown: `fx-tests task <id>` on Android jobs could give each test's slot. That is `start_test`'s `selectedProcess`: the harness knows it but does not log it. Logging it in `remotexpcshelltests.py` would make this trivial.

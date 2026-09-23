## Android "Failure details not recorded" hides the harness's own explanation

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_partialTraversal_01.js`
- Expected: a failure message for the 3 Android failures. The job log has one: `WARNING ... | Failed wait for remote log: <path> missing?` followed by `INFO remotexpcshelltests.py | <test> | <pid> | Could not read log file: ... No such file or directory`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`.
- Workaround: loaded the resource-usage profile and searched its INFO/WARNING markers for the test name.
- Suggestion: when an xpcshell FAIL has no structured message, fall back to the harness's WARNING/INFO lines that name the test (e.g. "Failed wait for remote log", "Could not read log file", "Timing out"). That alone would tell "the test process never produced a log" apart from a real assertion failure.

## Question: "which tests ran in parallel with this failure, and in which process slot?"

- Command: `profiler-cli thread markers --search launch_application --list --limit 0 --json` + a Python script that pulls `$iN` and `_TEST_NAME` out of each `am startservice` label.
- The default output truncates the label, so slot and test name (both deep in a ~3 KB command line) are not visible.
- `fx-tests task <id>` could list the tests whose run overlapped a failing test's run, with their slot and outcome. For Android, the logcat is where the process deaths are: `lowmemorykiller: Kill '...:xpcshellN' (<pid>)` and `Zygote: Process <pid> exited due to signal 9`. Linking those to the test that owned the slot would show LMK kills directly.

## Android tests killed by lowmemorykiller mid-run are reported PASS

- Not a fx-tests bug: the data is wrong upstream (see the report). But it means fx-tests counts these runs as passes. In TUzf4ujqSIqO2-2tX9R7xw, `dom/streams/test/xpcshell/large-pipeto.js` was SIGKILLed during its second task and is PASS. In LSK4EYQIRMCr30Nykht1Yw.1 four other tests were killed and are PASS. A logcat cross-check (LMK kill of a slot while a test owned it) would find these hidden failures.

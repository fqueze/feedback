## `--bugs` finds nothing when the bug names the job's failure, not the test

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_eventemitter_basic.js --bugs`
- Question: "which bug were these failing jobs starred on?"
- Expected: a line saying no bug names the test, and ideally the bug(s) sheriffs starred the failing tasks with (here bug 1991833, "High frequency win-msix PermissionError: [WinError 5] Access is denied", 10 annotations in 7 days).
- Got: the normal `test` output with no bugs section and no "none found" line, so it was unclear whether the flag did anything.
- Workaround: Bugzilla quicksearch on the traceback text found bug 1991833; `fx-tests intermittent --bug 1991833` then confirmed it.

## `task` does not surface the job-level harness exception

- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ --profiles`
- Question: "why did 755 of 920 tests fail in this job?"
- Got: 755 per-test TIMEOUT rows. The one fact that explains all of them, the harness's `Following exceptions were raised:` / `PermissionError: [WinError 5] Access is denied` traceback from `CreateProcess`, is not shown.
- Could show: a job-level line for harness ERROR markers not tied to a test (tracebacks), next to the outcome counts.
- Workaround: loaded the resource-usage profile and searched for `Traceback`.

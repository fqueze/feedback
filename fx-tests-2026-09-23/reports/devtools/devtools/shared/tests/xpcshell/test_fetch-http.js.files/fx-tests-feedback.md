## Android harness launch failures are reported as "Failure details not recorded"

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_fetch-http.js` (Issues section), and `fx-tests task Ymuq-fpUShWTYiHu_2sDaw --profiles`
- Expected: the failure mode to name what happened, which the job's resource-usage profile has as an INFO line: `remotexpcshelltests.py | Failed to start process: devtools/shared/tests/xpcshell/test_fetch-http.js | 0 | Could not kill left-over process`.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`, and `task` printed no message at all for the failing execution.
- Workaround: loaded the resource-usage profile and searched its markers for the test name. The harness line is attributable (it names the test), so the issue text could carry it, and several tests in each Android job fail with the same line.

## (review) `task` does not surface the harness exception that ended the job

- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ` (msix, 750 TIMEOUTs)
- Question: why did 750 tests time out at once?
- Expected: the job's harness-level error, which the resource-usage profile has as an ERROR marker after `Following exceptions were raised:` — `PermissionError: [WinError 5] Access is denied` from `_winapi.CreateProcess` in `launchProcess`.
- Got: 750 rows of `TIMEOUT — Test timed out` / `Timed out and was force-killed by the harness`; nothing about the traceback (no match for "Access is denied", "Traceback", "PermissionError" in the output).
- Workaround: `profiler-cli thread markers --search Traceback`. A job-level "harness exceptions" line in `task` would have named the cause at once; the per-test TIMEOUTs are an artifact (the 45 s kill timer is not cancelled when launchProcess raises).

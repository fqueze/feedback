# fx-tests feedback (test_stack.js)

## Android launch failure shown as "Failure details not recorded"

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_stack.js`
- Expected: the failure message of the Android run (W27WjjL_REOcVL0PdasVXQ).
- Got: `FAIL    Failure details not recorded (likely Android or platform logging issue)`.
- In the job's resource-usage profile, the test's run holds `remotexpcshelltests.py | Failed to start process: devtools/shared/tests/xpcshell/test_stack.js | 0 | Could not kill left-over process` (an INFO line, because the harness logs it with `log.info`). Picking up `Failed to start process:` INFO lines inside a FAIL test's span would name this mode, which other Android xpcshell tests also have.
- Workaround: load the resource-usage profile and search `left-over`.

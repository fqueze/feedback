## Android xpcshell failure shown as "Failure details not recorded" although the harness logged the reason

- Command: `fx-tests test devtools/platform/tests/xpcshell/test_nsjsinspector.js`
- Expected: the failure message, which the job's resource-usage profile has as an INFO line: `remotexpcshelltests.py | Failed to start process: devtools/platform/tests/xpcshell/test_nsjsinspector.js | 0 | Could not kill left-over process`.
- Got: `2x FAIL Failure details not recorded (likely Android or platform logging issue)`.
- Workaround: loaded the resource-usage profile and searched markers for the test name.
- Also: with that message recorded, `fx-tests failures --message "Could not kill left-over"` could say how many Android tests fail this way; today `fx-tests failures --message "Failure details not recorded"` matches nothing either.

## The failure message of an Android xpcshell harness launch failure

- Question: what message did this Android failure have?
- Command: `fx-tests test devtools/shared/network-observer/test/xpcshell/test_security-info-certificate.js`
- Expected: the failing run's message, e.g. the harness INFO line `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`, which the job's resource-usage profile holds as a TestStatus marker at the test's end.
- Got: `Failure details not recorded (likely Android or platform logging issue)`. Also `fx-tests failures --harness xpcshell --message "Failure details not recorded"` then says no failure matched, though `test` shows that exact label under Issues.
- Workaround: loaded the resource-usage profile and searched its markers for the test name.

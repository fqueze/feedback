## Android harness launch failures show as "Failure details not recorded"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_LabelAndShallowSize_03.js` and `fx-tests task VwK5CynQSpy-vFLKYzU6UA.0 --profiles`
- Expected: the failure detail of the two Android failures, which the job's resource-usage profile holds as a TestStatus INFO line during the test's run: `remotexpcshelltests.py | Failed to start process: Only one instance of an application may be running at once` (VwK5CynQSpy-vFLKYzU6UA) and `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` (P6IC7EXkRuC_U4Ur_Zq42A).
- Got: `Failure details not recorded (likely Android or platform logging issue)`, and in `task` no message at all for 6 of the 7 failures in VwK5.
- Workaround: load the resource-usage profile and search `Failed to start process`. The "Only one instance" line does not name the test, so it has to be matched to the test by time.

## `failures --message` cannot find the placeholder text `test` prints

- Command: `fx-tests failures --harness xpcshell --message "Failure details not recorded"`
- Expected: the tests whose failures `fx-tests test` reports under that text, to see how many other tests fail the same way.
- Got: `No failure matched.`
- Workaround: none; the scope of the harness failure across tests stayed unknown.

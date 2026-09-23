## Android failure shown as "Failure details not recorded" when the harness logged why

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_deduplicatePaths_01.js` (and `fx-tests task bfrX05-pRkmY-a2l9vj1-w --profiles`, `fx-tests task W27WjjL_REOcVL0PdasVXQ --profiles`)
- Expected: the failure message for the two Android runs, which the job's resource-usage profile has as an INFO marker in the test's time range: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` in `test`, and no message at all under the test in `task`.
- Workaround: load the resource-usage profile and `thread markers --search "<test file>" --list`.
- Cost: this message is the whole diagnosis for these failures; with it, `fx-tests` alone would have grouped the 6 tests in these 2 jobs failing the same way.

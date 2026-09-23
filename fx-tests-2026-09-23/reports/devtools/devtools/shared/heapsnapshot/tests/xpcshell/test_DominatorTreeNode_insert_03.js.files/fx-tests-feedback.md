## What did this failing Android run actually report?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_insert_03.js` and `fx-tests task NKmJhtrXQ8Sf_fjYxIRF5Q.1 --profiles`
- Expected: the failure message of the failing execution.
- Got: `Failure details not recorded (likely Android or platform logging issue)`; `task` prints only `FAIL — 1 failing execution of 2` and no message.
- The answer was in the job's resource-usage profile, as an INFO marker in the test's own time range: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. `task` could show the harness INFO lines between a failing test's start and end (or the ones that name the test) when there is no TEST-UNEXPECTED line.
- Workaround: loaded the resource-usage profile and ran `thread markers --search <test file name> --list`.

## Which tests and jobs hit this one harness message?

- Command: `fx-tests errors --harness xpcshell --message "left-over"` (for 2026-09-14 and 2026-09-20), and `fx-tests failures --harness xpcshell --message "Failure details not recorded"`
- Expected: the other tests and jobs failing with `Could not kill left-over process`.
- Got: no matches. Harness INFO lines are not in the errors file, and the "Failure details not recorded" issue text is not in `failures` (these runs are probably under `(no message recorded)`, 9,189 failures across 2,071 tests, with no breakdown).
- Workaround: none. I could not say how often this message hits Android xpcshell jobs across the tree.

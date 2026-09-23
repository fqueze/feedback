## `test --bugs` prints nothing when no bug matches
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_04.js --bugs`
- Expected: a "Bugs" section, even if it only says "none found".
- Got: the same output as without `--bugs`, with no bug section. I couldn't tell "no bugs" from "flag ignored".
- Workaround: searched Bugzilla REST with quicksearch myself.

## Question: "how did my test fail in this job?" (a 1154-failure job)
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: a way to get one test's row, e.g. `--test <path>`.
- Got: a truncated list that did not reach my test. I needed `--limit 0` (4,634 lines) plus grep. The header could also have said the job was broken as a whole ("1152 of 1552 tests TIMEOUT").
- Workaround: `--limit 0 > file; grep -A4 <test>`.

## Question: "what was the failure message?" on Android ("Failure details not recorded")
- Command: `fx-tests test <path>` (Issues) and `fx-tests task VwQ7va9ITWmZ2dd-62av4A --profiles`
- Expected: the harness's own reason.
- Got: "Failure details not recorded (likely Android or platform logging issue)". The resource-usage profile has the answer as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. The `task` output could surface `Failed to start process` INFO lines for tests with no recorded failure message.
- Workaround: loaded the resource-usage profile and searched for the test name.

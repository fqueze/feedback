## `errors --message` does not match the location it prints

- Command: `fx-tests errors --harness xpcshell --message "nsAHttpConnection.cpp:57"`
- Expected: the rows for the C++ warning raised at `netwerk/protocol/http/nsAHttpConnection.cpp:57`, which is how the task log and profile markers identify it.
- Got: `No markers matched.` The same command with `--message "sts->Dispatch(event.forget()"` finds it, and then prints `netwerk/protocol/http/nsAHttpConnection.cpp:57` under "Where they come from".
- Workaround: search by the warning text, not the file:line.
- Suggestion: let `--message` also match `file:line`, or say in the no-match output that only the message text is searched.

## Question: "in which jobs did this test print this warning?"

- Command: `fx-tests errors --harness xpcshell --day 2026-09-19 --message "sts->Dispatch(event.forget()" --json`
- Wanted: the task IDs behind "3 occurrences in test_webtransport_stop_sending.js", to check them against the 2 crash jobs `fx-tests test --task-ids` lists for that day (each crash log has exactly one occurrence). The third could be a run that leaked without crashing, which would matter for the diagnosis.
- Got: counts per test only. The JSON has no task IDs either.
- What would have answered it: task IDs (or `--task-ids`) per test row.

## `test --bugs` prints nothing when there is no bug

- Command: `fx-tests test netwerk/test/unit/test_webtransport_stop_sending.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the default output, identical to running without `--bugs`, so it is unclear whether the search ran.

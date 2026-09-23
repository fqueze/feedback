## `fx-tests test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_breakpoint-11.js --bugs`
- Expected: a line such as `Bugs: none name this test` (the JSON has `annotatedBugs: []`).
- Got: the same output as without `--bugs`, with no bug section at all, so "no bug" looks the same as "the flag was ignored" or "the section was cut off". I ran it three times with different `tail`/`sed` windows before checking `--json`.
- Workaround: `--json` and read `annotatedBugs`.

## `fx-tests task` shows different messages for the same failure

- Command: `COLUMNS=300 fx-tests task <taskId> --profiles --limit 0` on the six msix jobs.
- Expected: the same message for test_breakpoint-11.js in each job, since it failed the same way in all of them.
- Got: `Timed out and was force-killed by the harness; ...` in four jobs, and `Test timed out` in PyUxuOpdQj2b3T3XyQOKpg and LKys8j8iTQKTAN9hKv040g. The same job's list mixes both messages across tests. The profiles show the same `Timed out and was force-killed` ERROR in all of them. So the difference comes from which of the test's messages fx-tests picks, not from what happened.
- Cost: one extra profile load to rule out a second failure mode.
- Workaround: load the resource-usage profile and search for the test's name.

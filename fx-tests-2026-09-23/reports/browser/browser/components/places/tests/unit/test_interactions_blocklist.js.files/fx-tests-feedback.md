## `test --bugs` is silent when no bug names the test
- Command: `fx-tests test browser/components/places/tests/unit/test_interactions_blocklist.js --bugs`
- Expected: a line such as "No bug names this test".
- Got: the same output as without `--bugs`; nothing says the lookup ran. Had to use `--json` to see `annotatedBugs: []`.
- Workaround: `--json | jq .annotatedBugs`.

## Question: "is this TIMEOUT really a timeout?" (the harness exception behind a whole job)
- Command: `fx-tests task ZgUlY0ZlRvmHZNTOfia3AA --profiles`
- Expected: the job-level harness error surfaced next to "274 TIMEOUT": the `Following exceptions were raised:` traceback (`PermissionError: [WinError 5] Access is denied` from CreateProcess), which is why every test "timed out".
- Got: 274 rows of `TIMEOUT — Test timed out`; the traceback only shows up in the resource-usage profile (ERROR marker at the end of run-tests).
- Workaround: load the resource-usage profile and search `PermissionError`.

## Question: "this test's row in a job with hundreds of failures"
- Command: `fx-tests task <taskId> --profiles` (truncates), then `--limit 0` and grep.
- Expected: a `--test <path>` filter on `task`.
- Workaround: `--limit 0 | grep -A3 <test>`.

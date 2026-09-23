## `fx-tests task <id> --profiles` on a job where 1152 of 1552 tests TIMEOUT does not say why

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Question: "why did every test in this job fail at once?"
- Expected: the job-level cause. The resource-usage profile holds it: the harness's own `ERROR` marker after `Following exceptions were raised:` (`PermissionError: [WinError 5] Access is denied` from `_winapi.CreateProcess`), and the time the cascade started (1,461 `will retry` markers between t=50s and t=56s).
- Got: the list of 1154 failing tests, 20 at a time, each "TIMEOUT — Test timed out", and "All 1154 failed only in the parallel phase".
- Workaround: loaded the resource-usage profile, dumped all markers with `--json`, and scripted over them to find harness-level markers (those not naming a test) and the traceback.
- Suggestion: when most of a job fails, print the harness-level ERROR markers (not attributed to a test) and the start of the failure burst.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-07.js --bugs`
- Expected: a line saying no bug names the test.
- Got: the same output as without `--bugs`; no Bugs section at all, so "none found" and "flag ignored" look the same.

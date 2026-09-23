## Issue text says "details not recorded" when the job log has the reason

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_05.js`
- Expected: the Android failure's issue to carry the harness line explaining it.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`. The job's resource-usage profile (task R7FPh5v5RDyClCEfWPly1Q) has an INFO marker `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` for this test, i.e. the test never started.
- Workaround: loaded the resource-usage profile and searched the test name.
- Question it could have answered: "why did this Android run fail with no failure message?" Surfacing `remotexpcshelltests.py | Failed to start process` INFO lines as the issue text would answer it directly, and would let `fx-tests failures --message "Failed to start process"` group the harness-level launch failures tree-wide.

## `failures --message` does not match the issue text `test` prints

- Command: `fx-tests failures --harness xpcshell --message "Failure details not recorded"` and `--message "force-killed by the harness"`
- Expected: the rows `fx-tests test` listed under Issues for this test.
- Got: `No failure matched.`
- Workaround: none; gave up on the tree-wide count.

## `task` does not say the job broke wholesale

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: a hint that the job failed as a whole (1152 TIMEOUT out of 1552, 1149 of them starting within 4 s and lasting 45 s each), so the row for my test is collateral.
- Got: the counts, then a list of individual failing tests; the "they all timed out together" fact needed the profile plus a script.
- Question: "did this job break as a whole, and when?" A line like "N tests timed out in one burst starting at t=…" would answer it.

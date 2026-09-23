## `errors --message` cannot match on a numeric error code

- Command: `fx-tests errors --harness xpcshell --day 2026-09-10 --message "Error:-2147009284" --task-ids`
- Expected: the jobs whose logs have `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)` (0x80073CFC, the MSIX package needing remediation).
- Got: "No markers matched." The stored text is normalized to `(Error:-<num>)`, so the number, which is the discriminator between launch failures, cannot be searched, and the normalized row mixes all codes (a separate `(Error:0)` row shows only 0 escapes normalization).
- Workaround: `--message "Failed to launch"` and then checking each task by hand.

## `test --bugs` does not find the job-level bug behind a mass-timeout job

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-06.js --bugs`
- Question: "which bug covers this failure?" The only failure was one of 1,152 TIMEOUTs in one job (PyUxuOpdQj2b3T3XyQOKpg.0), which sheriffs star on bug 1991833 (win-msix PermissionError).
- Got: no bug. `fx-tests task` shows "1152 TIMEOUT" but does not say "this job failed wholesale; its failures are not the test's". A flag on `test --task-ids` rows whose job had >N% failing tests would have told me at step 1 that this is a job-level failure.
- Workaround: Bugzilla quicksearch on "msix Access is denied".

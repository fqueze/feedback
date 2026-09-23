## `test --bugs` prints no bug section at all
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_SaveHeapSnapshot.js --bugs`
- Expected: a "Bugs" section, even if it says "none name this test".
- Got: the plain `test` output, with no mention of bugs, so I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla quicksearch on the job's harness error (found bug 1991833, the job-wide bug, which names no test).

## `errors --message` cannot find an exact error code, because numbers are normalized
- Command: `fx-tests errors --harness xpcshell --message "Error:-2147009284" --day 2026-09-10 --task-ids`
- Expected: the `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)` rows (this code, 0x80073CFC, is the discriminator for the MSIX breakage).
- Got: "No markers matched"; searching the text part shows the row as `(Error:-<num>)`, merging all error codes, and the `Task IDs` section it printed was empty.
- Workaround: read the resource-usage profiles directly.
- Question: "which jobs hit this exact launch error code" — the row could keep the distinct codes and their task IDs.

## `task` does not say that the job was a job-wide harness abort
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: a line saying the harness raised `PermissionError: [WinError 5] Access is denied` and aborted, and that 1,149 of the TIMEOUTs never launched a process (45 s timer, "not killing -- proc or pid unknown").
- Got: "1154 failing, 1152 TIMEOUT" and a per-test list, which reads as 1,152 real timeouts.
- Workaround: loaded the resource-usage profile and searched `Traceback`, `not killing`.

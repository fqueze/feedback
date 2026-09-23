## `errors --message` does not match numbers in the raw message

- Command: `fx-tests errors --harness xpcshell --day 2026-09-10 --message "2147009284"` (also `--message "Error:-2147009284"`, `--message "0x80073cfc"`)
- Expected: the "Failed to launch tab subprocess @CreateProcess (Error:-2147009284)" markers, which are in the job's resource-usage profile.
- Got: "No markers matched." The file stores the message normalized as `(Error:-<num>)`, and `--message` only matches the normalized text, so an error code, the most specific part of the message, cannot be searched for.
- Workaround: `--message "Failed to launch"`, which found it with task IDs.

## Question: "did my test's failure come from a job that broke wholesale?"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-05.js` (and `--task-ids`)
- Its one failure is a TIMEOUT in a job (PyUxuOpdQj2b3T3XyQOKpg) where 1,152 of 1,552 tests timed out; nothing in `test` hints at it. I only learned it from `fx-tests task`, whose header says "1154 failing".
- What could have shown it: next to each task ID in `--task-ids`, the job's failing-test count (or a "mass failure: N tests failed in this job" flag), so a one-off in a broken job is not diagnosed as the test's own intermittent.

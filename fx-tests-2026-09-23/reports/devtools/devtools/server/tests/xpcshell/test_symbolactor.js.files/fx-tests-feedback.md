## `fx-tests task` shows a different failure message for the same failure, depending on the job

- Command: `COLUMNS=300 fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles --limit 0 --messages --full-messages`
- Expected: for devtools/server/tests/xpcshell/test_symbolactor.js, the same row as in the 5 other broken msix jobs: `1x devtools/server/tests/xpcshell/test_symbolactor.js | Timed out and was force-killed by the harness; ...`
- Got: `Test timed out` (no `1x` prefix). The profile holds the same `ERROR ... Timed out and was force-killed by the harness` marker in the replayed log (m-6) as in the other jobs, and the `test` marker's Message is `Test timed out` in all of them. So which message is shown varies by job without any real difference, and read like a second failure mode.
- Workaround: loaded the resource-usage profile and compared the `test` marker and the replayed-log ERROR marker in two jobs.

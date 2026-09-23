## `errors --message` silently misses numbers, which are normalized to `<num>`

- Command: `fx-tests errors --harness xpcshell --day 2026-09-10 --message "2147009284"` (and `--message "0x80073cfc"`)
- Expected: the `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)` rows, which appear in the profile verbatim.
- Got: "No markers matched." The stored message is `... (Error:-<num>)`, so a numeric error code can never match, and nothing says so.
- Workaround: search on the text around the number (`--message "Failed to launch"`).
- Suggestion: match `--message` against the normalized form after normalizing the query the same way, or print a hint when the query contains digits.

## Question: "was this failure part of a job-wide breakdown?"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_deleteHeapSnapshot_03.js`
- It reports `1x TIMEOUT Test exceeded time limit` on msix and calls the test intermittent. Only `fx-tests task` then shows that the job had 1,152 TIMEOUTs out of 1,552 tests, i.e. the test was one of a thousand collateral failures.
- What could have shown it: a per-failure flag in `test` (and `--task-ids`) when the failing job's failure count is far above normal, e.g. "job had 1,154 failing tests".
- Related, for the cross-job view: "which jobs of this config broke the same way" took `errors --message ... --task-ids` for each day in turn, then `task` on every ID. A multi-day `errors` query, or `failures`, keyed by message with task IDs would answer it in one command.

## `fx-tests test <path> --task-ids` does not flag a failing job that was a job-wide breakdown
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_02.js --task-ids --limit 0`
- Expected: next to the task ID, a hint that the job itself was broken, e.g. "job had 1154 failing tests of 1552". That alone classifies the failure as environment/infra.
- Got: just the task ID and config. I only found out by running `fx-tests task`.
- Workaround: `fx-tests task <id>`, reading its "1154 failing" header.

## `fx-tests test <path> --bugs` prints nothing when no bug matches
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_02.js --bugs`
- Expected: a line like "No bugs name this test".
- Got: exactly the output without `--bugs`, so it is ambiguous whether the lookup ran.
- Workaround: a Bugzilla REST quicksearch.

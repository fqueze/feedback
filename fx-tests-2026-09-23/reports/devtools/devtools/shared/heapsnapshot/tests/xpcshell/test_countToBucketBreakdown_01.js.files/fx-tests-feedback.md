## `test --bugs` prints no bug section when there is none

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_countToBucketBreakdown_01.js --bugs`
- Expected: a line such as `Bugs: none found naming this test`.
- Got: the same output as without `--bugs`, so I could not tell "searched, found nothing" from "flag ignored".
- Workaround: searched Bugzilla's REST API myself.

## `task --profiles`: no way to go to one test's row

- Question: "what does this job say about my test?" (job with 1,154 failures)
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles --limit 0 > file; rg countToBucketBreakdown_01 file`
- Expected: a `--test <path>` filter.
- Got: the whole list, truncated by default; my row was only reachable with `--limit 0` and grep.

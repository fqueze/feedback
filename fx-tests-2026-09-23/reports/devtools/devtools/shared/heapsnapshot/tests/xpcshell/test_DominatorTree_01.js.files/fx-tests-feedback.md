## `task` has no per-test filter

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: a way to see my test's row (outcome, messages, whether a per-test profile exists).
- Got: the first 20 of 1,154 failing tests; my test was not among them.
- Workaround: `--limit 0` into a file, then `rg test_DominatorTree_01` (row 972 of ~3,800 lines).
- Question: "what happened to this one test in this job, and is there a profile for it?" A `--test <path>` filter on `task` would answer it.

## A TIMEOUT whose process never started looks like any other TIMEOUT

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_01.js`
- Expected: some sign that this TIMEOUT was a launch failure, not a slow test.
- Got: `TIMEOUT Test exceeded time limit`.
- Workaround: in the resource-usage profile, joined the `test` markers with the `<id> failed or timed out, will retry.` INFO markers over `--json` with a script. 1,149 of the job's 1,153 TIMEOUTs logged "will retry" less than 1 s after they started, which means `run_test` raised during launch and the kill timer reported the TIMEOUT 45 s later.
- Question: "did this timed-out test ever start?" A flag on the issue row when "will retry" comes within about 1 s of test_start would answer it.

## Question: "is this test failing on its own, or only as part of job-wide meltdowns?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_watchpoint-04.js --task-ids --limit 0`
- Expected: some hint that every one of the 6 failing jobs had hundreds of other tests failing too (476 to 1563 TIMEOUTs per job), which is the key fact for this test.
- Got: verdict "intermittent, fails on 1 configuration", 6 task IDs, nothing about the jobs' other failures. Needed 6 separate `fx-tests task <id> --profiles --limit 0` calls to see it.
- Could have shown: per failing task, the job's failing-test count (e.g. "JOcV... 1061 failing in this job"), or a verdict line such as "all 6 failures are in jobs where >400 tests failed".

## `fx-tests task --profiles` default truncation hid the test I asked about

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles | grep test_watchpoint-04`
- Expected: the entry for the test that led me to this job.
- Got: nothing; the FAILED list is truncated alphabetically ("... 1041 more"), so the test was not in it.
- Workaround: `--limit 0` (4,000+ lines) and grep. A `--test <path>` filter, or always including the entry for a test passed in, would answer it directly.

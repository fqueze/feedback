## Question: "are this test's failures part of job-wide breakdowns?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_source-02.js --task-ids --limit 0`
- Expected: some signal that each of the 6 failing jobs had hundreds of other failing tests (476 to 1,563 TIMEOUTs each), which changes the diagnosis from "this test times out" to "this test never ran".
- Got: only task IDs and config; I had to run `fx-tests task <id>` six times and read each header line (`Outcomes, counted per test: 1060 TIMEOUT ...`).
- Workaround: loop over `fx-tests task`. A per-task "N other tests failed in this job" column in `--task-ids` would answer it at once.

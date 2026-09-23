## Question: "was this test's failure one of hundreds in its job?" (job-wide meltdown)

- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-08.js --task-ids --limit 0`
- Expected: some hint, per failing task, of how many other tests failed in the same job. All 6 failing jobs had 476-1563 TIMEOUTs each (the MSIX package stopped launching); the test itself never ran.
- Got: 6 task IDs and "TIMEOUT Test exceeded time limit", with a verdict of "intermittent" on one config. It took one `fx-tests task <id>` per task to find out each job had melted down.
- Workaround: `fx-tests task <id> | head -8` for each task ID.
- Could show: a per-task "N other tests failed in this job" column, or a verdict line like "all 6 failures are in jobs where >400 tests failed".

## `fx-tests task --profiles` on a 488-failure job

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: to find my test's entry and whether it had a profile.
- Got: the first 20 of 488 failures in alphabetical order. My test was not among them, so I needed `--limit 0 | rg`.
- Workaround: `COLUMNS=400 fx-tests task <id> --limit 0 --profiles | rg -A4 <test>`.
- Could show: a `--test <path>` filter on `fx-tests task`.

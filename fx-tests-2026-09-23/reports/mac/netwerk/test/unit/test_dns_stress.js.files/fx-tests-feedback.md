## Question: which of this test's failures happened in jobs where nearly every test failed?

- Command: `fx-tests test netwerk/test/unit/test_dns_stress.js` then `--task-ids --limit 0`
- Expected: some marker on each failing run (or a per-config note) saying the job itself collapsed, e.g. "16 of 16 failures on test-windows11-64-25h2/opt-xpcshell-msix were in jobs with >= 475 other TIMEOUTs".
- Got: the per-config table ranks windows11 msix as the worst config at 4.5%, and the Issues list merges those with the real test-specific timeouts as one `TIMEOUT Test exceeded time limit` issue (26x).
- Workaround: ran `fx-tests task <id>` on all 27 failing tasks in a loop and read the "Outcomes, counted per test" line of each (27 calls, several minutes).
- What its output could have shown: the count of other failing tests in the same job next to each task id in `--task-ids`, or a "job-wide failure" flag.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test netwerk/test/unit/test_dns_stress.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the same output as without `--bugs`, so it is unclear whether the search ran.
- Workaround: queried Bugzilla REST directly.

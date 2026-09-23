## A passing run of the test on the failing config, to compare timings

- Question: give me a task where `test_console_assert.html` passed on `test-linux2404-64-tsan/opt-mochitest-chrome-1proc`, to compare the timing of a background timer against the test in a passing first run.
- Command: `fx-tests test <path> --task-ids` (lists failing tasks only); `fx-tests test --help` has no option for passing tasks.
- Expected: e.g. `--task-ids --passed` or `--coverage --task-ids` listing a few passing task IDs per config.
- Got: no way from fx-tests. The 222 runs are spread over jobs whose manifest set varies (test selection), so a random job of that config usually did not run the test.
- Workaround: Treeherder REST `jobs/?job_type_name=...` for 40 recent jobs, then `fx-tests task <id> --passed --limit 0` on each: none of the 40 had run the test except a failing one. Gave up on the comparison.

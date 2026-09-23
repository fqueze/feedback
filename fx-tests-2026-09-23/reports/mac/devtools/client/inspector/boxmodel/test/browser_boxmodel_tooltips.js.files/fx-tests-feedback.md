## Question: the failure messages, task IDs and profiles of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`, then searching the output for the test name.
- Expected: the test's row, or a clear sign that it did not fail.
- Got: no match. The PERMA-FAILS section shows 10 of 51 rows (`… 41 more (--limit 0 for all)`), and this test was among the hidden ones. With a push unskipping hundreds of tests, a missing row reads as "did not fail" unless the truncation line is noticed.
- Then: `fx-tests try <rev> --all-jobs --test <path> --task-ids` answered "failed in 4 of 4 jobs", but printed no task IDs, messages or profile URLs despite `--task-ids`, and took several minutes.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0 --messages --full-messages` into a file, then grep. Slow, and ~1000 lines to get one row.
- What would have answered it: `--test <path>` filtering the default (failed-jobs) report down to that test's row, with its messages, task IDs and profile URLs; or `--test` honouring `--task-ids`/`--profiles`.

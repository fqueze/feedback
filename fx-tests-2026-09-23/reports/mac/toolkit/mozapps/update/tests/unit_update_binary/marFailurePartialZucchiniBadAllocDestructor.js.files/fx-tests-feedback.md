## Question: which other tests of the same directory failed in each of this test's 22 failing jobs?

- Command: a loop of `fx-tests task <taskId>` over the 22 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping for `unit_update_binary`.
- Expected: the full list of failing tests per job.
- Got: `fx-tests task` truncates the FAILED list at 20 (`… 2 more (--limit 0 for all)`); in B0vabHM6QHukJn3RVRRLPQ (22 failures) the truncated part held the very test the task ID came from, so the first pass of the loop reported that job as not failing this test. My mistake to grep past the truncation line, but easy to make in a loop.
- Workaround: `--limit 0`.
- What could have shown it: `fx-tests test <path> --task-ids --co-failures <dir>` (per failing job, the other failing tests under a path), or `fx-tests task <id> --test <path>` that always lists the named test and its neighbours in the same directory.

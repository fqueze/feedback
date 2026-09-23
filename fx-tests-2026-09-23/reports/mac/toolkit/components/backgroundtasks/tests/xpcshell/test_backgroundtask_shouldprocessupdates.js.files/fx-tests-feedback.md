## Question: "what else failed, and how, in every job where this test failed?"

- Command: a shell loop of `fx-tests task <taskId>` over the 22 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping each for `toolkit/crashreporter` rows and their `CRASH, TIMEOUT` status (22 calls, several minutes).
- Expected: one command listing, for a test's failing jobs, the tests that co-failed with it and their status, ranked by how many of the failing jobs they appear in. Here it would have shown at once that 22 of 22 failing jobs have a crashreporter test with `CRASH, TIMEOUT`, and `test_backgroundupdate_exitcodes.js` failing with `11 == 21` in 19.
- The converse matters as much: jobs where the suspect leaker failed but this test passed (I had to pick task IDs from the leaker's `--task-ids` and run `fx-tests task <id> --passed --limit 0 | rg <test>` per job).

## `task --passed` truncates the passing list

- Command: `fx-tests task NmlWQS3zQIqXC4rOhXFyWw.0 --passed | rg shouldprocessupdates`
- Expected: the row for the test, passing.
- Got: nothing, because the DID NOT FAIL list is truncated by default; `--limit 0` was needed. A `--test <path>` filter on `task` would answer "did test X pass in this job" directly.

## (review) Question: "which of this test's timeouts also reported a crash?"

- Command: `fx-tests test toolkit/crashreporter/test/unit/test_crash_terminator.js --config macosx --task-ids --limit 0`
- Expected: the header, Issues block or task list to show that 10 of the 432 timeouts were `CRASH, TIMEOUT`, as `fx-tests task` shows per job.
- Got: `432 timeout   0 crash`, and a single Issues row `432x TIMEOUT`. The CRASH part of `CRASH, TIMEOUT` is dropped, and here it was the variable that told the leaking jobs from the harmless ones.
- Workaround: a script running `fx-tests task <id> --passed --limit 0` on all 432 tasks (about 2 s each, parallelized) and parsing the status line under the test's row. A per-task status column in `--task-ids`, or an Issues row for `CRASH, TIMEOUT`, would have answered it directly. Combined with the earlier "did test Y pass in these jobs" question, one `fx-tests test <X> --task-ids --with <Y>` would have replaced 577 `task` calls.

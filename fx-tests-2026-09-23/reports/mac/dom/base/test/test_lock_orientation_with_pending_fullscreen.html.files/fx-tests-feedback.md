## The failing tasks of one test on a try push: `--limit 0` does not expand the task list

- Command: `COLUMNS=400 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --task-ids --messages --limit 0`
- Expected: all 16 task rows for `dom/base/test/test_lock_orientation_with_pending_fullscreen.html`, since `--limit 0` means no limit.
- Got: 5 task rows, then `… 11 more tasks`. `--limit 0` applies to the rows, not to the task list inside a row.
- Workaround: `--json`, then a script over `permaFails[].taskIds` and `permaFails[].profiles`.
- The question the default output could not answer was "which tasks, and which profile URLs, are behind this one test's failures". Each task is also printed twice, once per failing execution, without saying which is the first run and which the retry. One row per task with `first + retry failed` would answer it, together with both per-test profile URLs (`…fullscreen.html.json` and `…fullscreen-2.html.json`). Today `--profiles` lists only the first.

## Which failure message came from which job

- Command: `fx-tests try <rev> --task-ids --messages`
- Question: which jobs produced the less frequent message (`Fullscreen request should be canceled.`, 8x)? It turned out to split exactly by config (non-xorig vs xorig), and that was the key clue.
- Got: message counts for the whole row, with no per-config or per-task attribution. Answering it took one `fx-tests task <id> --messages` per task (8 calls).
- Could show: per-message config counts, e.g. `8x Fullscreen request should be canceled. (debug-mochitest-plain-2 ×4, opt-mochitest-plain-4 ×4)`.

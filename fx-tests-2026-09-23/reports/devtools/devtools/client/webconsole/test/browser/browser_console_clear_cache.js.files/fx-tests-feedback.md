## The tasks that ran one test on a try push, pass or fail

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_clear_cache.js --all-jobs --task-ids` (and the same with `--json`).
- Expected: the task IDs behind each config's `jobs` / `passed` counts, so that one passing job's profile can be opened to check the test really ran there.
- Got: counts only. `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: took the asan task IDs from the unrelated failures listed in the default `fx-tests try --task-ids` output.

## Job counts disagree between the failed-only and `--all-jobs` views of `--test`

- Command: `fx-tests try 2888bcab0070 --test <path>` versus the same with `--all-jobs`.
- Expected: the failed-only view's `jobs` to be at most the `--all-jobs` view's, since failed jobs are a subset.
- Got: `test-linux2404-64/opt-mochitest-devtools-chrome-5` and `...-a11y-checks-5` show 4 jobs in the failed-only view and 3 in `--all-jobs`. One of these tasks appears as run `.1` (`e6fPXv0dTUWlpLrNpuNcMw.1`), so a task retry is probably counted as a job in one view only.
- Workaround: trusted the `--all-jobs` counts.

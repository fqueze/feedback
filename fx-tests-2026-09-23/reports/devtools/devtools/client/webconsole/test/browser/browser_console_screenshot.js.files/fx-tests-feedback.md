## Question: which task IDs ran this test on each config of a try push (to load their resource-usage profiles)?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_screenshot.js --all-jobs --task-ids` (also with `--json`)
- Expected: the task IDs behind each config row (passed / passed on retry / failed), since `--task-ids` was given.
- Got: only per-config counts; `--task-ids` is silently ignored in `--test` mode, and the JSON has no task IDs either.
- Workaround: ran the full `fx-tests try <rev> --task-ids --profiles --limit 0` (1,400 lines) and grepped task IDs of the same config out of other tests' failures. Only works when the job failed for some other test.

## Job counts differ between `--test` with and without `--all-jobs`

- Command: `fx-tests try 2888bcab0070 --test <path>` vs the same with `--all-jobs`
- Got: without `--all-jobs`, `test-linux2404-64/opt-mochitest-devtools-chrome-5` and `...-a11y-checks-5` show 4 jobs each; with `--all-jobs`, 3 each. Probably a task with two runs (e.g. `e6fPXv0dTUWlpLrNpuNcMw.1`) counted twice in one view, but neither output says why.
- Expected: the same job count, or a note on what the extra job is (a rerun of the same task, a job that did not reach the test).

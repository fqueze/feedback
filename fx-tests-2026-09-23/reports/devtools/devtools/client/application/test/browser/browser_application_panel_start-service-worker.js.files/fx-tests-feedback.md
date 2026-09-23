## Question: the task IDs of the jobs in which a test passed on a try push (to check in their resource-usage profile that it actually ran)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/application/test/browser/browser_application_panel_start-service-worker.js --task-ids --profiles --limit 0` (and the same with `--json`)
- Expected: per configuration, the task IDs (and resource-usage profile URLs) of the job runs counted in `passed`, since `--task-ids` and `--profiles` were passed.
- Got: only the per-config counts table (jobs / passed / passed on retry / failed); `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: for linux asan, grepped the job name out of the failure listing (the chunk had other failing tests); for windows asan, queried `https://treeherder.mozilla.org/api/jobs/?push_id=...` by hand to get the task IDs, then `fx-tests task <id> --profiles` for the resource-usage URL.

## Question (review): has this test failed anywhere since its skip landed, beyond the 21-day window?

- Command: `fx-tests test <path> --history` (21 days), then `fx-tests intermittent --bug 1781479` (default 7 days, trunk: "no sheriff annotations").
- Expected: a way to see a test's last failure, or the bug's last annotation, however old, when the recent window is clean.
- Got: both clean. The report concluded "no failure since then that CI data can show". A 2-of-2 timeout on 2026-07-15 (autoland, windows11-64-25h2 debug standalone) only showed up with `fx-tests intermittent --bug 1781479 --since 520 --tree all`.
- Suggestion: when the window is clean, `fx-tests test` (or `--history`) could print the date of the last annotated failure of the test's bugs.

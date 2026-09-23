## Question: which task IDs ran (and passed) this test on a try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_open_or_focus.js --task-ids` (and the same with `--json`)
- Expected: the per-config table plus the task IDs (and resource-usage profile URLs with `--profiles`) of the jobs that ran the test, so a passing run can be opened in the profiler to confirm it ran.
- Got: only the per-config counts table; `--task-ids` is silently ignored, and the JSON has no task IDs either.
- Workaround: took the asan task IDs from another test's failure list in the default `fx-tests try` output (same job ran both manifests).

## Question: which task IDs ran this (passing) test on a try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_clear_closed_tab.js --task-ids --profiles`
- Expected: per config, the task IDs (and resource-usage profile URLs) of the jobs that ran the test, so its passing runs can be checked in a profile.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with `--test`, and `--json` has no task IDs either.
- Workaround: `fx-tests try <rev> --task-ids --profiles --limit 0 --json` and a script walking every failure row for jobName matches — which only finds jobs that failed for some other test. A job where everything passed would have no way to be found.
- Also: in the text output, `--limit 0` does not lift the per-row task list truncation (`… 37 more tasks`), so the Windows asan task IDs were only reachable via `--json`.

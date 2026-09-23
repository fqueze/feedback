# fx-tests feedback

## `try --test` ignores `--task-ids`

- Question: which tasks are behind the failures of one test on a try push.
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/shared/test/browser_filter-editor-07.js --task-ids` (also tried with `--json`).
- Expected: the task IDs (and with `--profiles`, profile URLs) of the 3 failing a11y-checks-1 jobs, next to the per-config table.
- Got: only the per-config table (jobs/passed/passed on retry/failed); no task IDs, in text or JSON.
- Workaround: `fx-tests try <rev> --task-ids --profiles --json` and a script to find the test's row in `permaFails` (the text output had truncated it among "... 25 more").

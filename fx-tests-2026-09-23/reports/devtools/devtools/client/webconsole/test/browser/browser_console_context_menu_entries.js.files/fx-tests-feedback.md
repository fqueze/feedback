## Question: "which jobs (task IDs) of this try push ran this test, and passed it?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_context_menu_entries.js --task-ids --profiles` (also with `--json`)
- Expected: per config, the task IDs (and resource-usage profile URLs) of the job runs that ran the test, so a pass can be checked in a profile.
- Got: only the counts table (jobs/passed/passed on retry/failed). `--task-ids` and `--profiles` are silently ignored with `--test`; the JSON has no task IDs either.
- Workaround: `fx-tests try <rev> --task-ids --json` and a script walking every row for `taskId` with a matching `jobName` (works only because those jobs failed on other tests).

## `--limit 0` does not expand the per-row task list of `fx-tests try`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --limit 0`
- Expected: every task ID under each row.
- Got: rows still end with `… 37 more tasks`, and no flag to expand them is named. The Windows asan dt-2 task IDs were only in the `--json`.
- Workaround: `--json` plus a script.

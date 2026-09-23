## `fx-tests try --task-ids --limit 0` still truncates the task list

- Question: the failing tasks of one test on a try push, one per job, grouped by config.
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --messages --limit 0 --full-messages --profiles`
- Expected: `--limit 0` lists all 42 task rows and 21 profile rows for the test.
- Got: `… 37 more tasks` and `… 16 more profiles` under each test, with no flag to expand them.
- Workaround: `--json` and a script over `permaFails[].taskIds`. `--test <path>` gives the per-config table but no task IDs.

## `fx-tests try --json`: `profiles[].testProfiles` leaves out the retry profile

- Command: `fx-tests try 2888bcab0070 --json`, entry for `browser_dbg-scroll-run-to-completion.js`
- Expected: both `profile_browser_dbg-scroll-run-to-completion.js.json` and `...-2.js.json`, since `outcomes.failedTwice` is 21.
- Got: one URL per task. `fx-tests task ak4eiOv9SbO_yp9m6cZWFw --profiles` does list both.
- Workaround: `fx-tests task <id> --profiles`.

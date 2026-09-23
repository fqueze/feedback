## Question: which pushes ran this test, and passed, after its last failure (to find the landing that stopped it)

- Command: `fx-tests test devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_process_main_local.js --config standalone --task-ids --json`
- Expected: some way to get the passing runs' task IDs / revisions for one config over a date range, since `--history` shows the test stopped failing on 2026-09-17 and the brief asks for the landing that stopped it.
- Got: `taskIds` only lists failing tasks; per-day counts have no revisions.
- Workaround: queried Treeherder `api/project/autoland/jobs/?job_type_name=...-standalone-<chunk>` for every chunk, then ran `fx-tests task <id> --passed --limit 0 | rg <test>` on each candidate job to find which chunk ran the test. Several minutes and many calls.
- What would have answered it: `fx-tests test <path> --config X --runs` (or `--task-ids --passed`) listing each run's day, revision, push time, task ID and status, so the first passing push after the last failure is one command.

## Question: does this failure follow the worker it ran on

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadLegacy.js --task-ids --limit 0 --issue 4`, then `fx-tests task <id> --messages` per task.
- Expected: the worker ID of each failing task, and a way to see whether a failure mode is concentrated on a few machines. Here the macOS xattr failure is on 5 of ~140 workers and fails there every time; that was the whole diagnosis.
- Got: task IDs and job names only. Workaround: one `curl .../task/<id>/status` per task for `workerId`, then Treeherder `jobs/?machine_name=<worker>` per worker and tree to list the other jobs on that machine, then `fx-tests task <id> --json` on ~250 jobs to read whether `test_DownloadLegacy.js` was in `passed` or `failures`. About 30 minutes and several scripts.
- Could have shown: a worker column in `--task-ids`, and a `--by-worker` view (failures and runs per worker) in `fx-tests test`.

## `--task-ids` text output lists sequential-retry failures as a second, unlabelled list

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadLegacy.js --task-ids --limit 0 --issue 4`.
- Expected: one row per failing execution with its status, or two labelled groups.
- Got: `Task IDs (168 jobs, ...)` grouped by date, then, with no header, a second date-grouped list repeating the 14 macOS task IDs. The JSON shows the second list is the `FAIL-SEQUENTIAL` entries (182 entries = 168 jobs + 14 retries). In the text it reads like a duplicated listing, and the fact that carried the diagnosis (these 14 jobs failed the retry too, every time) is invisible.
- Workaround: `--json` and group by `status`.

## Question: what was the outcome of one test in one job

- Command: `fx-tests task <taskId>` over ~250 jobs.
- Expected: a way to ask for one test's outcome in a job (`--test <path>`), PASS / FAIL / not run.
- Got: the default output lists failures only; whether the test passed or did not run at all needed `--json` and a search of `passed[].path` and `failures[].path`.

## Question: "which worker ran each failing task?"

- Command: `fx-tests test uriloader/exthandler/tests/mochitest/browser_open_internal_choice_persistence.js --task-ids --limit 0`
- Expected: the worker (machine) name next to each task ID, or a note that failures cluster on few workers.
- Got: task IDs and job names only. All 11 failing trunk jobs (and all 15 annotated on the bug) ran on two workers, macmini-m4-80 and macmini-m4-92, which was the key finding of this diagnosis.
- Workaround: one `curl .../api/queue/v1/task/<id>/status` per task for `runs[0].workerId`. A "machines" column, or a "failures concentrated on N workers" verdict line, would have shown it immediately.

## Question: "did this test run on this worker, and did it pass there?"

- Commands: Treeherder `api/project/<tree>/jobs/?machine_name=<worker>` for the job list, then `fx-tests task <id> --json` per job, with a Python filter over `failures[]`/`passed[]` for the test path.
- Expected: something like `fx-tests test <path> --machine <worker>` (or a per-worker pass/fail table under `--coverage`), answering "is this worker always failing it?".
- Got: nothing in fx-tests reaches per-worker outcomes; the passing executions are not attributed to machines.
- Workaround: a shell script over 240 jobs (`check_task.sh` in this directory). It showed 11/11 fails on those two workers from 2026-09-07 to 2026-09-15, then passes on both on 2026-09-21/22.

## Question: "are this test's failures confined to a few CI machines?"

- Commands: `fx-tests test uriloader/exthandler/tests/mochitest/browser_open_internal_choice_persistence.js --task-ids --limit 0`, then `fx-tests task <id> --passed` per job.
- Expected: the worker (machine) name next to each failing task ID, and ideally, per worker, how many runs of the test passed and failed.
- Got: task IDs only. Every one of the 11 failing jobs (each failing its retry too) turned out to run on 2 of 169+ macOS 15 workers (macmini-m4-80, macmini-m4-92), which is the whole diagnosis for an environment failure, but nothing in fx-tests shows it.
- Workaround: `curl .../api/queue/v1/task/<id>/status` for `workerId` of each failing task; the Treeherder `jobs/?machine_name=` API to list each machine's jobs; then a shell loop of `fx-tests task <id> --passed --limit 0` over 209 jobs to find which ran the test and whether it passed. Several minutes and a script.
- What would have answered it: a worker column in `--task-ids`, and a line in the verdict when failing jobs concentrate on few workers ("11 of 11 failing jobs ran on 2 workers").

## `intermittent --bug` default window hides non-trunk annotations

- Command: `fx-tests intermittent --bug 1776017`
- Expected: the bug's recent annotations.
- Got: exit 2 "no sheriff annotations for bug 1776017 on trunk between 2026-09-16 and 2026-09-22", while `--since 30 --tree all` lists one on mozilla-esr153 on 2026-09-16 and 30 overall.
- Workaround: `--since 30 --tree all`. The message did suggest it, so this cost only one call.

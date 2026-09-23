## Question: "on which configs does this crash signature happen, across all its tests?"

- Command: `fx-tests crashes --signature "child process hang at shutdown"` (and `--json`).
- Expected: a per-config breakdown for the signature (19,349 crashes, 503 tests).
- Got: "This file records no job names, so nothing here can be broken down by configuration", and the JSON `tests` array stops at 50 entries.
- Workaround: looped `fx-tests test <path> --json` + `--task-ids --issue <n> --limit 0` over the top 50 tests (100 invocations) and aggregated `jobName` myself. A `crashes --signature X --by-config` that does this fan-out would have answered it.

## Question: "is this failure concentrated on some workers?"

- Command: `fx-tests test <path> --task-ids`, `fx-tests task <taskId>`.
- Expected: the worker (machine name) of each failing task, and a denominator of jobs per worker.
- Got: no worker anywhere in the output.
- Workaround: Taskcluster `/task/<id>/status` per task for `workerId`, and Treeherder `/api/project/<repo>/jobs/?job_type_name=...` (its `machine_name`) for all jobs, including the green ones. It turned out to be the whole answer: 22 of 78 macOS 10.15 workers produce 96% of this signature. A `--by-worker` view on `test --task-ids` (fail count / job count per workerId) would have shown it directly.

## Trap: `pid` / `process_uptime` in a macOS child hang dump are not the child's

- Command: reading the processed dump JSON of `fx-tests crash <task> <dump> --raw` (hang dumps written on request).
- Observed: dump 29D7F036 (GPU process, per its modules and threads) has `pid` 1590, while ProcessWatcher crashed pid 1653; mozcrash prints `Process pid: 1590`. `process_uptime` then tracks the test's duration (≈ start of the xpcshell process), not the GPU process's. I first derived "GPU launched at test start" from it and had to retract it.
- Suggestion: when the main module is a child executable and the dump was requested (the HANG heuristic already fires), say that pid/uptime may describe the dump writer, not the dumped process.

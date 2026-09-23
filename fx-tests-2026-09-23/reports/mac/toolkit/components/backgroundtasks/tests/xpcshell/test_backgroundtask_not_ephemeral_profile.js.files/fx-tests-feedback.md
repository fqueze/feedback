## What ran on this worker before this job? (state left on a reused machine)

- Question: the failure was machine state left by an earlier task on the same macOS hardware worker. I needed the worker of each failing job, then the xpcshell jobs that ran on that worker before it, with their revision/version and the test's outcome in each.
- Commands: `fx-tests test <path> --task-ids --limit 0`, `fx-tests task <taskId>` — neither prints the worker (workerGroup/workerId).
- Workaround: Taskcluster `task/<id>/status` for the worker id, then the Treeherder jobs API `api/project/<repo>/jobs/?machine_name=<worker>&start_time__gte=...` over try/central/autoland/beta, plus a Python script to fetch each push's `config/milestone.txt`. About 10 calls and two scripts.
- What could have shown it: the worker id in `fx-tests task` and `--task-ids` output, and something like `fx-tests worker <workerId> --before <taskId> --harness xpcshell` listing earlier jobs on that machine, with their repo/revision and the given test's outcome.

## Outcome of one test in many jobs

- Question: "did test X pass, fail or not run in job Y", for about 40 jobs.
- Command: `fx-tests task <id> --passed --limit 0`, then grep. A failing test is listed in the FAILED block in a different layout from the PASS table, so one regex could not read both, and "absent" was ambiguous: not in this chunk, or failed.
- What could have shown it: a `--test <path>` filter on `fx-tests task` printing one line (status, number of executions, first message), or "not run in this job".

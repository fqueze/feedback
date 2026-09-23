## Question: "does this failure follow particular CI workers?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_blackboxing-08.js --task-ids --limit 0`
- Expected: some way to see the worker (machine name) behind each failing task, and how many jobs of that config each worker ran in the window, so a worker-specific failure shows at a glance.
- Got: task IDs only; no machine names, and no list of the passing jobs to compare with.
- Workaround: Taskcluster `task/<id>/status` for the failing tasks' `workerId`, then the Treeherder jobs API (`/api/project/<repo>/jobs/?job_type_name=...`, paginated, filtered locally by date) for every job's `machine_name`. Here it showed 107 failures on 22 of 78 workers and none in 950 jobs on the other 56 — the key fact of the diagnosis, and invisible from fx-tests.
- Could have shown: a per-worker column (or `--by-worker`) in `fx-tests test` for a failing config: jobs, failing jobs.

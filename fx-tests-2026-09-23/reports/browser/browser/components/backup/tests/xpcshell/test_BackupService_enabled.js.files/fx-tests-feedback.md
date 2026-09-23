## Question: which workers do the failing runs of this test run on, and how does that compare with the pool?

- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_BackupService_enabled.js --task-ids --limit 0`
- Expected: the worker (machine name) next to each failing task, and ideally a per-worker pass/fail breakdown for the config.
- Got: task IDs only. The answer here turned out to be "22 of 79 macOS 10.15 workers produce every failure" — which changes the Kind to environment — but getting it took a Taskcluster status call per failing task, a Treeherder jobs query for the pool, and one artifacts listing per pool job (1,373 calls) to count `.dmp` files per worker.
- Workaround: `curl .../task/<id>/status` for workerId; Treeherder `/api/project/<repo>/jobs/?job_type_name=...&last_modified__gt=...` for `machine_name`; count `.dmp` artifacts per job.
- What would have answered it: a `--workers` view on `fx-tests test` (fails/runs per worker for each failing config), or the machine name in `--task-ids`.

## Question: which process is this "child process hang at shutdown" dump from?

- Command: `fx-tests crash cQxb-TYVRe2XTgcDoc_UZA.0 E97683A0-0022-4596-BAAF-35F1F86441B0`
- Expected: the process type (GPU here) in the header, since for this signature the dump is always of a child, not of the test's process.
- Got: no process type; I found it from the `Firefox Nightly GPU Helper` frame at the bottom of the main thread (needed `--frames 0`). The harness log even prints `Process type: main` for it.
- What would have answered it: the executable / ProcessType line in the header, and for hangs the threads' innermost non-system frames summarized (here: main thread in `nsThread::Shutdown: Renderer`, Renderer in `RenderThread::InitDeviceTask` -> LaunchServices).

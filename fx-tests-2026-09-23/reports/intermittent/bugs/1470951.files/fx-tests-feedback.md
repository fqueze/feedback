## Question: which jobs of one job type on one worker pool passed and which failed, with worker and start time (harness-startup hang, no test path)

- Bug 1470951 is a harness-level signature (`REFTEST ERROR | None | application timed out after 370 seconds with no output`) that names no test.
- `fx-tests intermittent --bug 1470951 --limit 0` lists only the failing (starred) tasks. `fx-tests task <id> --profiles` says "This profile records no tests at all", which is correct but ends there. `fx-tests test` needs a test path.
- What I needed: the passing population of `test-macosx1500-aarch64-vms/opt-{crashtest,jsreftest-N}` over the window, and for every job (pass or fail) its workerId, start time and a couple of log facts, to compare failing and passing runs.
- Workaround: Treeherder API (`/api/project/<tree>/push/?push_timestamp__gte=..`, then `/api/jobs/?push_id=..`), filtered on platform prefix and job name, then Taskcluster `status` + `system-info.log` + a streamed read of `live_backing.log` per task (scripts `collect_more.py`, `collect_sysinfo2.py` here). About 15 minutes of work.
- What could have shown it: something like `fx-tests job <job-name> --platform <p> --since <d>` listing each job's result, task id, workerId and start time.

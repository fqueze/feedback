## The task IDs of the jobs where a test passed

- Question: which jobs ran this test and passed, so I can load a passing job's resource-usage profile and compare it with the failing one (was the parallel phase also at 100% CPU when the test passed?).
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test toolkit/components/downloads/test/unit/test_DownloadBlockedTelemetry.js --task-ids` (also with `--json`)
- Expected: the task ID of each of the 4 jobs counted in the table (2 debug, 2 opt, all passed).
- Got: only the per-config counts; `--task-ids` adds nothing, and the JSON has no task IDs either. `fx-tests test <path> --task-ids` likewise lists only failing tasks.
- Workaround: Treeherder REST API for the push's jobs.
- What could show it: the task IDs next to the counts, when `--task-ids` is given with `--test`; and for `fx-tests test`, a few passing task IDs per config (the parallel-phase duration too, since that is the comparison one wants).

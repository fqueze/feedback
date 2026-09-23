## Question: "was this job a job-wide collapse, and what was its first failure?"
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (and the other five failing tasks of this test).
- Expected: a note that 487 of 550 tests TIMEOUT'd within a second of each other, the chronologically first failure, and the harness traceback that ended the run (`PermissionError: [WinError 5] Access is denied` from `launchProcess`).
- Got: 488 failing tests in alphabetical order, each "Test timed out". Nothing says they all started at the same instant or that the harness aborted.
- Workaround: downloaded `live_backing.log` for each task and scripted over it (first "will retry" line, tests still running at that moment, the traceback).

## Question: "a passing run of this test on the same config, to compare with"
- Command: none available; `fx-tests test <path> --task-ids` lists only failing tasks.
- Expected: a way to get one or two passing task IDs per config (e.g. `--task-ids --passing`).
- Workaround: Treeherder API `jobs/?job_type_name=...&result=success`.

## Question: "which bug are these failing jobs starred with?"
- Command: `fx-tests test <path> --bugs` printed nothing for this test.
- Expected: bug 1991833, which all 6 failing jobs are classified with on Treeherder (the bug does not name the test).
- Workaround: Treeherder `bug-job-map/?job_id=` per job.

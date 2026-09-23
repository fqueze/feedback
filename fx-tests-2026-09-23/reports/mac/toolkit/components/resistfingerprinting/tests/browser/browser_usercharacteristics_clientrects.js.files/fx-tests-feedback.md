## A passing job that ran the test, to compare with the failing ones

- Question: in jobs where this test passed, does the resource-usage profile show the same `[ERROR glean_core] Glean should not be initialized multiple times` line during the manifest's first test? (It is in all 31 failing jobs.)
- Commands: none in fx-tests. `fx-tests test <path> --task-ids` lists failing tasks only, `--coverage` gives configs but no task IDs.
- Workaround: Treeherder `api/project/<repo>/push/` and `api/jobs/?push_id=..&job_type_name=..`, then grep each job's `profile_resource-usage.json` for the test path to find the chunk that ran the manifest (5 passing jobs found this way, none with the line).
- What would have answered it: `fx-tests test <path> --task-ids --passing --limit N` (task IDs of runs where the test passed, per config), or a way to get the task that ran a given manifest on a given push.

## "Which of the job's failures co-occur" across all failing jobs

- Question: does this test always fail together with the same other tests in the job?
- Commands: `fx-tests task <id> --profiles` for each failing task (31 calls).
- What would have answered it: a co-failure count in `fx-tests test <path>` ("in 31/31 of its failing jobs, css/gamepads/math/mathml/svg/timezone also failed").

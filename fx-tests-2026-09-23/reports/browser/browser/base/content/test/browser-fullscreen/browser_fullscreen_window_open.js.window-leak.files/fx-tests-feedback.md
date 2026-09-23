## The retention path of each leaked-window failure, per job

- Question: in each of the 13 jobs with `leaked window until shutdown`, what held the window (the ShutdownLeakPathFinder path)?
- Command: `fx-tests task <taskId> --profiles --messages` gives the three leak messages and "No failing test named a per-test profile in this job", but not the path. The path is the TEST-FAIL's stack (it is in the resource-usage profile as the FAIL marker's stack, and in the live log).
- Expected: the stack of `leaked window until shutdown` next to the message, or a pointer to the resource-usage marker that has it.
- Workaround: downloaded the 13 live logs and grepped the lines after `Shutdown - leaked window until shutdown`.

## Passing runs of the test in the same browser state

- Question: the task IDs of passing runs of this test on the failing configs (non-standalone Windows 11 debug), to compare with the failing first runs rather than with the fresh-browser retries.
- Command: `fx-tests test <path> --task-ids` lists failing tasks only; `--coverage` gives counts only.
- Expected: a way to get a few passing task IDs per config (e.g. `--task-ids --status pass --limit 3`).
- Workaround: the push's Taskcluster task-group listing, filtered on `MOZHARNESS_TEST_PATHS` containing the manifest.

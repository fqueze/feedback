## Question: did every failing run happen on a push from before fix X landed?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --json` for each of the 31 tasks, and `hg json-pushes?changeset=<rev>` for each revision, scripted.
- Expected: one command to date a "stopped failing" verdict against a landing.
- Got: `--task-ids` gives the day the job ran, not the revision or its push time. The 3 failures dated 2026-09-14 were jobs run that day on 2026-08-15 pushes, which the per-day `--history` makes look like a recurrence after the fix.
- What the output could show: the revision (and push date) next to each task id in `--task-ids`, or a `--history` keyed by push date; and `passedOnRerun` per task, which is only in `task --json`.

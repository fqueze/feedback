## Question: the revision of each failing run (did any failure land on a push that has a given fix?)

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the repo and revision next to each task ID, since the task ID alone cannot be placed relative to a landing.
- Got: task IDs and job names only; I ran `fx-tests task <id>` 13 times and grepped `revision=` out of the Treeherder URL line of each.
- Could have shown: `<taskId> <job> <repo> <rev>` per row (the `task` command already knows it).
- Side note: `fx-tests task b5SwIOIoQi2_O6Yn3l7C5w` warned "could not read the definition of task", so that one's revision is unknown.

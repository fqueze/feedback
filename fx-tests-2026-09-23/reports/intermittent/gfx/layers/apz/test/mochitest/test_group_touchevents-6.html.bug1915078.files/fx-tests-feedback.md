## Question: which of this test's failing tasks have a per-test profile?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --profiles` on each candidate.
- Expected: the task list to mark which tasks uploaded a per-test profile for this test (Android geckoview jobs never do here; linux wayland debug jobs always do).
- Got: 65 task ids with no profile indication; had to probe tasks one by one to learn that the 56 Android failures have none.
- Workaround: loop `fx-tests task <id> --profiles | rg profile` over tasks. A `P` flag per task id in `--task-ids`, or a `--with-profiles` filter, would answer it in one call.

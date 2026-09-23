## Question: are the failures clustered on a few pushes?

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the revision each failing task ran on, next to its task ID.
- Got: task IDs and job names grouped by date only; I had to run `fx-tests task <id>` on each of 9 tasks to see that all 9 were on two autoland revisions (194fd5be9ced, 8964e73d124a), which is what pinned the failure to one patch that was backed out.
- Workaround: a loop over `fx-tests task <id> --profiles | head`.
- What would have answered it: the repo and short revision on each `--task-ids` row, and a note when all failures share very few revisions.

## Question: how many runs of this test passed on those same pushes?

- Command: none available; `fx-tests test` has no per-revision breakdown.
- Expected: runs and failures per revision (or per push) for a test.
- Got: nothing; I queried the Treeherder API directly for job counts per push, which does not say which jobs ran this test.
- What would have answered it: `fx-tests test <path> --revision <rev>` or a per-push breakdown in `--history`.

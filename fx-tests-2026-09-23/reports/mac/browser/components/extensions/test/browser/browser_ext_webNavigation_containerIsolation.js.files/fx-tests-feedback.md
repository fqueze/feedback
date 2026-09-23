## Did these failures run on revisions before or after a fix?

- Command: `for t in <15 task ids>; do fx-tests task $t | sed -n 2p; done`, then the hg pushlog per revision.
- Expected: `fx-tests test <path> --task-ids` to print each task's repo and revision (and push date) next to its task id, since the date column is the job's run date, not the revision's: three 2026-09-14 failures ran on 2026-08-15 revisions and looked like post-fix recurrences until I fetched each task.
- Got: task ids and job names only.
- Workaround: one `fx-tests task` call per task, then `json-pushes?changeset=<rev>` and `json-log?rev=<fix>::<rev>` on hg.mozilla.org.

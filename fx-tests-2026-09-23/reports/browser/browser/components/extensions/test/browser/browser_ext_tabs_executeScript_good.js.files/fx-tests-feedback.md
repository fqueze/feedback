## Question: "a passing job of the same config and chunk, to compare its resource-usage profile"

- Command: `fx-tests test <path> --task-ids --limit 0` lists only failing tasks; `fx-tests test --help` has no way to list passing ones.
- Needed: whether a machine-wide ~2.5 GB disk-write burst seen in the failing jobs' resource-usage profiles also happens in passing jobs.
- Workaround: a script over the Treeherder API (`/api/project/autoland/push/?id__lt=…` then `/api/jobs/?push_id=…&job_type_name=…`) walking 60 pushes, then `fx-tests task <id>` on the hits.
- What would have answered it: `fx-tests test <path> --task-ids --passing` (or a few passing task IDs per failing config in `--task-ids`), so a failing and a passing resource-usage profile of the same config can be compared.

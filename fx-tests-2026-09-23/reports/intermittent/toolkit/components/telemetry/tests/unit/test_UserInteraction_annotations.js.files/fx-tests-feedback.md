## `test --task-ids --limit 0` prints unlabeled groups

- Command: `fx-tests test toolkit/components/telemetry/tests/unit/test_UserInteraction_annotations.js --task-ids --limit 0`
- Expected: one list of failing jobs, or groups with a header saying what each group is (per issue, or "jobs that failed twice").
- Got: under a single `Task IDs (65 jobs)` header, three date-sorted runs of task IDs one after another: the TIMEOUT jobs, then the FAIL jobs (issue 9 and 10), then 12 linux ccov jobs repeated from the first group, with no header between them. Dates restart from 2026-09-01 in each run, so it reads like a corrupted list.
- Workaround: `--issue <n>` per failure mode to learn which group is which; the third group I could only guess (jobs where both runs failed?).

## Question: which tasks ran this test and passed (to open their resource-usage profile)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/test/browser_tab_descriptor_fission.js --task-ids` (also with `--json`)
- Expected: the task IDs of the jobs counted in each row (3 passing jobs per config), since `--task-ids` was passed and `--all-jobs` had to read those very jobs.
- Got: only per-config counts (jobs/passed/passed on retry/failed); no task IDs in text or JSON.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=...`, then `/api/jobs/?push_id=...`) filtered on job_type_name.
- What the output could have shown: a task ID per job run in the `--test` table (and its duration for the test, which is what a skip removal wants to know: did it pass close to its timeout?).

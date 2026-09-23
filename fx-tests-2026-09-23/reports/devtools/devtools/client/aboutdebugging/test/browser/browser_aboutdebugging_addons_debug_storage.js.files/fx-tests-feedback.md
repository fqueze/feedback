## Question: on which jobs of a try push did this test pass, and with which task IDs?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --task-ids`
- Expected: for a test that failed on some jobs, the list of the jobs where it ran and passed (task IDs), so that a passing run's resource-usage profile can be compared with the failing ones.
- Got: `2/9` in the table, and task IDs only for the 2 failing jobs. The breakdown (7 jobs: 2 passed on retry, 4 passed, 1 not analyzed) is only in `--json` (`outcomes`), and no passing task ID appears anywhere, even in `--json`.
- Workaround: `--json` for the counts, then the Treeherder `api/jobs/?push_id=` endpoint filtered on the two job names to find the passing task IDs.

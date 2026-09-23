# fx-tests feedback — browser_net_filter-04.js

## `try --test --task-ids` prints no task IDs for passing runs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/netmonitor/test/filters/browser_net_filter-04.js --all-jobs --harness mochitest --task-ids`
- Question: which jobs ran this skipped test and passed, so the report can cite one.
- Expected: the task IDs behind each row of the per-config table.
- Got: the same table as without `--task-ids` (jobs / passed / passed on retry / failed), no task IDs.
- Workaround: none needed here; the report cites the configs and counts only.

## (review) No way to see whether a try push scheduled a config at all

- Command: `fx-tests try 2888bcab0070 --test devtools/client/netmonitor/test/filters/browser_net_filter-04.js --all-jobs`
- Question: did the push schedule any http3 job, so that "never ran on http3" means "not scheduled" rather than "scheduled and skipped by the manifest"?
- Expected: the `--test` table, or a footer, listing the scheduled test-job variants that did not run the test (not scheduled / skipped by manifest).
- Got: only the configs that ran it; absence is ambiguous.
- Workaround: Treeherder REST `api/jobs/?push_id=2044083&count=2000` and filtering `job_type_name` for `http3` (256 jobs, none).

## Question: which task IDs ran this test on the try push (to open their profiles)?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_evaluation_context_selector.js --all-jobs --profiles --task-ids`
- Expected: the task IDs (and resource-usage profile URLs) behind each row of the per-config table, since `--task-ids` and `--profiles` were passed.
- Got: only the per-config pass/fail table; `--task-ids` and `--profiles` are silently ignored with `--test`, and the `--json` has no task IDs either.
- Workaround: ran `fx-tests try <rev> --task-ids --profiles --json --limit 0` and regex-scraped job name / taskId pairs of other tests' failures from the same chunks. That only works when some other test failed in the job.

## Question: did the manifest this test is in hit a LeakSanitizer leak in this job?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles --full-messages` (and `fx-tests try <rev>`)
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures (9 of them, visible as ERROR markers in the resource-usage profile at t=23m23s) listed, attributed to the manifest.
- Got: "2 failing" — only the two test-level failures. Nothing in the text or `--json` mentions LeakSanitizer or the manifest. A skip-if whose reason is "frequent leaks on asan" (bug 1910261) therefore looks fixed from fx-tests alone.
- Workaround: loaded the resource-usage profile and searched `thread markers --search LeakSanitizer`.

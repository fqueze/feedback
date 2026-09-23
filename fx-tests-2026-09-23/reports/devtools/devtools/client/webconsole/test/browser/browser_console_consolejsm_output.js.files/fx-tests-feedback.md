## Question: which task IDs ran this test on the try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_consolejsm_output.js --task-ids --profiles --limit 0`
- Expected: the task IDs and resource-usage profile URLs behind each row of the per-config table.
- Got: only the pass/fail table. `--task-ids` and `--profiles` are silently ignored with `--test`, and its `--json` has no task IDs either. `--config` is refused on `try`.
- Workaround: `fx-tests try <rev> --task-ids --limit 0 --json`, then a script walking it for `taskId` next to a matching `jobName`. That only works because other tests failed in those jobs. (Same finding as in `browser_console_evaluation_context_selector.js.files/fx-tests-feedback.md`.)

## Question: did this test's manifest hit its LeakSanitizer leak in this job?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: the job's 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures, attributed to the manifest.
- Got: "2 failing", only the two test-level failures. The skip-if reason, "frequent leaks on asan", therefore looks resolved from fx-tests alone. `fx-tests try` does not show it either.
- Workaround: loaded the resource-usage profile and ran `profiler-cli thread markers --search LeakSanitizer --list`.

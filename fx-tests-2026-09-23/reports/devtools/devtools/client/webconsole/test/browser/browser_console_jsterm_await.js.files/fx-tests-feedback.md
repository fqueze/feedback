## Which task IDs ran (and passed) this test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_jsterm_await.js --all-jobs --profiles --task-ids`
- Expected: per config, the task IDs (and resource-usage profile URLs) of the jobs that ran the test, so a passing run can be opened in the profiler.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with `--test`, and `--json` has no task IDs either.
- Workaround: grepped the task IDs of the same job names out of the full `fx-tests try --task-ids` failure list, which only works because those jobs happened to fail on other tests.

## Manifest-level LeakSanitizer failures missing from `fx-tests task`

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: the 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at … | devtools/client/webconsole/test/browser/_browser_console.toml` lines, which make the job fail.
- Got: only the 2 per-test failures; the LSan failures (attributed to a manifest, not a test) are not listed.
- Workaround: loaded the resource-usage profile and searched markers for `_browser_console.toml`.

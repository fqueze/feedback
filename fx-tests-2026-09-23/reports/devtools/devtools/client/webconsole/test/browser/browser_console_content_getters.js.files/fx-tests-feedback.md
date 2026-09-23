# fx-tests feedback (browser_console_content_getters.js)

## The task IDs of the jobs in which a test passed on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_content_getters.js --all-jobs --task-ids` (and the same with `--json`)
- Expected: the task IDs behind each config row, so the passing jobs' resource-usage profiles can be read.
- Got: only the per-config counts table; `--task-ids` is silently ignored, and the JSON has no task IDs either.
- Workaround: grepped `fx-tests try <rev> --task-ids --limit 0` for the config name, which only works because those jobs happened to fail on other tests.

## Manifest-scoped LeakSanitizer failures are not listed

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg` and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --limit 0`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures (9 of them in that job) listed under FAILED, with the manifest as the path.
- Got: only the two per-test failures; the LSan failures appear nowhere, so the job looks as if the manifest's leak skip was not needed.
- Workaround: loaded the resource-usage profile and searched `LSan` markers.

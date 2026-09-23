## Manifest-level LeakSanitizer failures are invisible to `fx-tests try` and `fx-tests task`

- Question: "did the failure that this manifest's `skip-if = asan` guards against (Bug 1910261, LSan leak at the manifest's browser shutdown) come back on the try push that removed it?"
- Commands: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`, `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: a row for `devtools/client/webconsole/test/browser/_browser_console.toml` (or a "LeakSanitizer" row) in the linux asan chunk-2 jobs, since the job's resource-usage profile holds 11 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` ERROR markers and `LSan Leak` markers.
- Got: `fx-tests task` lists only 2 failing tests (browser_application_panel_open-links.js, browser_console_clear_method.js); `fx-tests try` has no row for the manifest. A skip-if removal whose whole purpose was a manifest-level leak looks clean.
- Workaround: load the resource-usage profile and `thread markers --search LeakSanitizer --list`.

## `fx-tests try --all-jobs --test <path> --task-ids` prints no task IDs

- Question: "which task IDs ran this test on each config (passing jobs included)?", to check the asan jobs for manifest-level failures.
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_dead_objects.js --task-ids` (and with `--json`)
- Expected: task IDs per config row.
- Got: only jobs/passed/passedOnRetry/failed counts; the JSON has no task IDs either.
- Workaround: took the task IDs of the same chunk from another test's failure row in `fx-tests try --json` (`permaFails[].taskIds`).

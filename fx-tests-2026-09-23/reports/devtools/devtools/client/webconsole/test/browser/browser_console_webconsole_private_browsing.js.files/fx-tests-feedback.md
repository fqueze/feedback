## `try --test` prints no task IDs (browser_console_webconsole_private_browsing.js report)

- Question: which jobs of the try push ran this test (to open their resource-usage profiles)?
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_webconsole_private_browsing.js --task-ids --profiles` (and `--json`)
- Expected: task IDs (and resource-usage profile URLs) per config row.
- Got: only per-config counts; `--task-ids`/`--profiles` silently ignored, and the JSON has no task IDs either.
- Workaround: ran `fx-tests try <rev> --task-ids --json --limit 0` and walked the JSON for jobName/taskId pairs of the asan configs. Only works when the job failed for another test; a fully green job would not be found.

## Manifest-level LeakSanitizer failure invisible in `task` and `try`

- Question: did the leak that the manifest's `skip-if = ["asan"]` (bug 1910261) was added for come back on this try push?
- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --messages`, and `fx-tests try <rev> --all-jobs --test <path>`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failure, attributed to the manifest, listed among the job's failures.
- Got: "189 tests ... 2 failing" with only two test rows; the LSan failures (10 TEST-UNEXPECTED-FAIL lines in the resource-usage profile) are not mentioned anywhere, and `try --test` reports every test in the manifest as passed.
- Workaround: loaded the resource-usage profile and searched markers for `LeakSanitizer`.

## Manifest-level LeakSanitizer failures are invisible in `fx-tests try`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids` and `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Question: did removing the manifest-wide `asan` skip of `devtools/client/webconsole/test/browser/_browser_console.toml` (skipped for LSan leaks, bug 1910261) bring its leak back?
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` rows, attributed to the manifest.
- Got: neither output mentions LeakSanitizer or the manifest; `fx-tests task` says "2 failing" (two tests) for a job whose resource-usage profile holds 9 LSan TEST-UNEXPECTED-FAIL markers for that manifest.
- Workaround: load each job's `profile_resource-usage.json` in profiler-cli and search `LeakSanitizer`.

## `fx-tests try --test <path> --task-ids` prints no task IDs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_content_object.js --task-ids --profiles` (and `--json`)
- Question: which jobs ran this passing test, so I can open their resource-usage profiles?
- Expected: the task IDs per config, as `--task-ids` promises.
- Got: only the per-config pass/fail table; the JSON has no task IDs either. Windows ASan task IDs for the chunk could not be found at all, since no test failing there was shared with this chunk's listing.
- Workaround: grepped task IDs for the same chunk name out of the failures listing of other tests.

## Question: did the manifest this test was unskipped in leak on the try push? (LSan manifest-level failure invisible)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --task-ids --limit 0`, and `fx-tests task SG5h3qzRTDi60GlyHb25Sg`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures (9 of them in that job's resource-usage profile at t=23m23s) listed, attributed to the manifest.
- Got: nothing. `task` lists only 2 failing tests; `try` has no LeakSanitizer row anywhere. The removed `skip-if` was exactly for this leak (bug 1910261), so "not in fx-tests" read as "the unskip is safe".
- Workaround: loaded the resource-usage profile and searched markers for `LeakSanitizer` / `LSan Leak`.

## Question: which task IDs ran this test on config X? (`--test` ignores `--task-ids`)

- Command: `fx-tests try <rev> --all-jobs --test <path> --task-ids --limit 0` (also `--json`)
- Expected: the task IDs behind each config row (needed to open the asan runs).
- Got: per-config counts only; no task IDs in text or JSON.
- Workaround: grepped task IDs for the config name out of the full `--all-jobs --task-ids` output.

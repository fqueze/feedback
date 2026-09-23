# fx-tests feedback (browser_console_content_object_context_menu.js)

## Question: the task IDs of the jobs where this test ran on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_content_object_context_menu.js --task-ids`
- Expected: the per-config table plus the task IDs behind each row (the test passed everywhere, so this is the only way to find the jobs that ran it).
- Got: the table only; `--task-ids` is silently ignored. `--json` has no task IDs either (only jobName/jobs/passed/failed).
- Workaround: took the task IDs of another test of the same manifest that failed in the same jobs (`browser_console_clear_method.js`) from `fx-tests try <rev> --task-ids --json`.
- What the output could have shown: one task ID per job run, per config row.

## Question: did the manifest-wide leak behind this skip-if reproduce on the try push

- The skip-if removed was the manifest DEFAULT `asan` (Bug 1910261, LeakSanitizer leak at shutdown of `_browser_console.toml`).
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0` and `fx-tests task SG5h3qzRTDi60GlyHb25Sg`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failure, attributed to the manifest.
- Got: nothing in either output. `fx-tests task` says "2 failing" and lists only two tests. Yet the job's resource-usage profile has these as ERROR markers and `LSan Leak` markers with `Scope: devtools/client/webconsole/test/browser/_browser_console.toml`.
- Workaround: downloaded the live_backing.log of each asan job and grepped it for `LeakSanitizer`, then loaded the resource-usage profile.
- What the output could have shown: manifest-scoped failures (LSan leaks, shutdown leaks) as their own rows, keyed by manifest path, so a skip-if on a manifest DEFAULT can be checked.

## `--limit 0` does not un-truncate task/profile lists

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: every task ID per row.
- Got: `… 37 more tasks` and `… 16 more profiles` per row despite `--limit 0`.
- Workaround: `--json`.

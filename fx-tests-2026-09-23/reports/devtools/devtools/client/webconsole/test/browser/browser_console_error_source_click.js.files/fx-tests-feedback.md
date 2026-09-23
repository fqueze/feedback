# fx-tests feedback

## Manifest-level LSan leak not reported by `task` or `try`

- Question: did the manifest this test is in leak on this push? (Its manifest-wide `skip-if = ["asan"]` exists for LSan shutdown leaks, bug 1910261.)
- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles` and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` (9 such lines, plus `LSan Leak` markers scoped to the manifest) listed as a failure of the manifest.
- Got: `task` lists only 2 failing tests (browser_application_panel_open-links.js, browser_console_clear_method.js); `try` has no row for the leak anywhere.
- Workaround: loaded the resource-usage profile and searched markers for `LeakSanitizer` / `_browser_console.toml`.

## `try --all-jobs --test` gives no task IDs

- Question: which tasks ran this test on this push, so I can open their profiles?
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_error_source_click.js --task-ids --profiles --limit 0` (and `--json`)
- Expected: task IDs (and resource-usage profile URLs) per config row, as `--task-ids` / `--profiles` do elsewhere.
- Got: only per-config counts; `--task-ids` and `--profiles` ignored, JSON has no task IDs either.
- Workaround: took the asan task IDs from another test's failure rows in the plain `try` output of the same manifest.

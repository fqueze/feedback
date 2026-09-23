# fx-tests feedback (browser_console_webextension.js)

## Manifest-scoped LeakSanitizer failures are invisible in `task` and `try`

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles` (also `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --limit 0`, and `fx-tests task ... --json`)
- Expected: the 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` lines listed as failures of the job (they are what the manifest's `skip-if = ["asan"]`, bug 1910261, exists for).
- Got: "2 failing" tests only (browser_application_panel_open-links.js, browser_console_clear_method.js); no mention of the leak anywhere, and `rg -i leaksanitizer` on the JSON finds nothing.
- Workaround: loaded the resource-usage profile and searched `TEST-UNEXPECTED` / `LSan Leak` markers on t-0.
- Question it should answer: "did the manifest this test belongs to leak in this job?" A test whose only skip-if is a manifest-wide leak skip reads as "no longer fails" from fx-tests alone.

## `try --test --all-jobs --task-ids` prints no task IDs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_webextension.js --all-jobs --task-ids` (and `--json`)
- Expected: the task IDs of the jobs that ran the test, per config (to open the passing asan jobs' resource profiles).
- Got: the per-config counts table only; `--task-ids` silently ignored, JSON has no task IDs.
- Workaround: took the asan task IDs from the unrelated rows of `fx-tests try <rev> --task-ids --limit 0`; this only works for jobs that failed for another reason.

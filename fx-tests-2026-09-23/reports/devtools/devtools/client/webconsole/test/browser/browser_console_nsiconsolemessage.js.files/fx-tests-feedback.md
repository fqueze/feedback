## `fx-tests try <rev> --all-jobs --test <path> --task-ids` prints no task IDs

- Question: which job runs ran this test on the ASan config, so I can check their logs/profiles for the manifest-level leak its skip-if was about?
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_nsiconsolemessage.js --task-ids` (and the same with `--json`)
- Expected: the task IDs (and resource-usage profile URLs) behind each config row, as `--task-ids` gives elsewhere.
- Got: only per-config counts; the JSON has no task IDs either.
- Workaround: took the ASan chrome-2 task IDs from the failure list of another test in the same chunk (`browser_console_clear_method.js`), which only works when some other test failed in those jobs.

## `fx-tests task` / `fx-tests try` do not show a manifest-level LeakSanitizer failure

- Question: did the ASan leak that `_browser_console.toml`'s manifest-wide `skip-if = ["asan"]` (Bug 1910261) was for recur on the try push that removed it?
- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --messages` (and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --limit 0`)
- Expected: a row for the 12 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` markers, which are in that job's resource-usage profile (attributed to the manifest, not a test).
- Got: only the two test-level failures (browser_application_panel_open-links.js, browser_console_clear_method.js); the leak is invisible, so a test whose skip-if was for a shutdown leak reads as cleanly passing.
- Workaround: grepped `live_backing.log` of each ASan job, then `profiler-cli thread markers --search LeakSanitizer` on the resource-usage profile.
- Correction to the entry above: the resource-usage profile holds 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer` markers (plus 3 related output/INFO markers, 12 matching `LeakSanitizer` in all), not 12 TEST-UNEXPECTED-FAIL.

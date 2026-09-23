# fx-tests feedback

## Manifest-level LeakSanitizer failures are invisible in `try` and `task`

- Question: "did the manifest this test's skip-if covers still leak on asan in this push?"
  (the skip is `asan` in `_browser_console.toml` [DEFAULT], Bug 1910261, LSan leaks).
- Commands:
  - `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
  - `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: a row for `devtools/client/webconsole/test/browser/_browser_console.toml` with
  `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ...`.
- Got: `task` lists only 2 failing tests (browser_application_panel_open-links.js,
  browser_console_clear_method.js); no LSan entry. `try` has no row for it either. The job's
  resource-usage profile does hold 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... |
  devtools/client/webconsole/test/browser/_browser_console.toml` markers and 30+ `LSan Leak`
  markers at t=23m22s.
- Workaround: loaded each asan job's resource-usage profile in profiler-cli and ran
  `thread markers --search LeakSanitizer --list`. Cost: 3 profile loads.
- What would have answered it: `task` listing manifest-scoped failures (LSan, shutdown leaks)
  alongside test failures, and `try --test <path>` saying "its manifest leaked in N of M jobs".

## `try --test ... --task-ids` prints no task IDs

- Command: `fx-tests try <rev> --all-jobs --test <path> --task-ids --profiles`
- Expected: the task IDs of the jobs behind each per-config row.
- Got: the per-config table only.
- Workaround: `fx-tests try <rev> --task-ids --limit 0 --json` and a regex over the JSON to find
  the Windows asan chrome-2 task IDs; the text output hides them behind "… N more tasks", and
  `--config` is refused for `try`.
- Question: "the task IDs of the jobs that ran this test on config X".

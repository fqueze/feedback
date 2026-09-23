## Manifest-level failures (LeakSanitizer at shutdown) are missing from `task` and `try`

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg` and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --limit 0 --full-messages`
- Expected: the job's 9 `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` lines listed, since `task` says "FAILED (2) — every test this job recorded a failure for".
- Got: only the two per-test failures (`browser_application_panel_open-links.js`, `browser_console_clear_method.js`). `try` does not mention LeakSanitizer or `_browser_console.toml` anywhere.
- Workaround: load the job's resource-usage profile and `thread markers --search LeakSanitizer --list`.
- Why it matters: the manifest's `skip-if = ["asan"]` (bug 1910261) exists for exactly this leak. Reading the fx-tests output alone, removing it looks safe on asan.

## `try --test` job counts change with `--all-jobs`

- Command: `fx-tests try 2888bcab0070 --test devtools/client/webconsole/test/browser/browser_console.js` vs the same with `--all-jobs`
- Got: `test-linux2404-64/opt-mochitest-devtools-chrome-5` and `...-a11y-checks-5` show 4 jobs without `--all-jobs`, and 3 jobs (3 passed) with it. The header says "jobs and failed are exact", so the two can't both be right.

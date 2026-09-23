## Question: which task IDs ran this test (and passed) on a try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_webconsole_ctrlw_close_tab.js --task-ids --limit 0` (and the same with `--json`)
- Expected: the per-config table plus the task IDs of the jobs behind each count, so a passing run can be opened (resource-usage profile) to confirm it ran and what the manifest reported (e.g. leaks).
- Got: only per-config counts; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: took the task IDs from another test of the same manifest that failed in those jobs (`browser_console_clear_method.js` row of `fx-tests try <rev> --task-ids --json`), which needed a script since the text output truncates the task list ("… 37 more tasks") even with `--limit 0`.
- Also: `--config` is refused on `try`, so there was no way to narrow the task list to the asan configs.

## Question: did the leak a manifest's asan skip is about come back on this try push?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`, and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --limit 0`
- Expected: the job's manifest-level failures listed too. That job's resource-usage profile has nine `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at … | devtools/client/webconsole/test/browser/_browser_console.toml` markers (Bug 1910261's signature).
- Got: `task` says "2 failing" and lists only two tests; `try` has no LeakSanitizer row for that manifest. A failure attributed to a manifest rather than a test does not show up anywhere.
- Workaround: loaded the three linux asan resource-usage profiles and searched for `TEST-UNEXPECTED-FAIL | LeakSanitizer` in each.

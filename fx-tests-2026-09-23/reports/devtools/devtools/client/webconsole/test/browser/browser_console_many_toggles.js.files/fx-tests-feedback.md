## `test --bugs` prints no bug section, and no "none found" line

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_console_many_toggles.js --bugs`
- Expected: the bugs whose summary names the test, here 1812593 (RESOLVED FIXED) and 1998960 (RESOLVED INCOMPLETE, 2026-09-16), or an explicit "no bug names this test".
- Got: the usual `test` output, identical to running without `--bugs`. No bug section at all, so I could not tell "none found" from "flag ignored".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_console_many_toggles"`.

## `try --all-jobs --test <path> --task-ids` prints no task IDs

- Question: "the task IDs of the runs where this test passed on the formerly skipped configs", so I could open one passing run's resource-usage profile to check its duration.
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_many_toggles.js --task-ids`
- Expected: a task ID per job run under each configuration row.
- Got: only the per-config counts table. `--task-ids` was silently ignored.
- Workaround: none. I did not open a passing profile.

## firefox-ui (Marionette harness) tests are invisible to `fx-tests test`
- Command: `fx-tests test testing/firefox-ui/tests/functional/safebrowsing/test_notification.py` (also `--history`, `--task-ids`)
- Expected: pass/fail counts per config and history for a test that fails ~5% of Windows fxfn jobs.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — nothing.
- Workaround: Treeherder REST (`/api/project/<repo>/jobs/?job_type_name=<...>&count=2000`) per job type, then `text_log_errors` per failed job to confirm which test failed; ~10 curl+python scripts to get denominators, the step change date and the regression range.
- Question it could have answered: "how often does this firefox-ui test fail, on which configs, since when, and which push first failed".

## `fx-tests task` reports "0 tests" for a firefox-ui job whose resource profile has 8 `test` markers
- Command: `fx-tests task Wo0kGeyhT5SQ2NsWx2f9bg --profiles`
- Expected: the per-test outcome table (the resource-usage profile has `test` markers with PASS/ERROR/SKIP for each test).
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus a warning that the ERROR marker "named no test path".
- Workaround: loaded the resource profile in profiler-cli and listed `name:test` markers.

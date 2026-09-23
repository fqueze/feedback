## Question: which test failures are in the jobs sheriffs starred on this bug?

- Command: `fx-tests intermittent --bug 2072442` (and `--json`, fields `lines` / `tests`)
- Expected: the failing tests of the 25 annotated jobs.
- Got: "Failure messages, per annotated job" lists only 3x test_cache_encryption_flip_purge.py and 1x test_backup_selectable_to_legacy.py (4 of 25 jobs). The other 21 jobs failed on `TEST-UNEXPECTED-ERROR | browser/components/sessionstore/test/marionette/test_new_tab_on_restore.py ...` (a timeout or "Process has been unexpectedly closed"), which appears nowhere, so the output reads as if 21 jobs had no test failure at all. For a bug whose failure line is a log assertion that sheriffs mis-star, this list is the key answer.
- Workaround: downloaded all 25 live_backing.log files and grepped TEST-UNEXPECTED-* myself.

## Question: is this log line (an assertion) also in green jobs, and since when? (marionette)

- Command: `fx-tests test <marionette .py path>`, `fx-tests errors --message "Cannot initialize a connection"`
- Expected: some way to get per-day counts of a log message in marionette jobs, green ones included.
- Got: `test` reports "No test path in the xpcshell and mochitest 21-day data"; `errors` only accepts `--harness xpcshell|mochitest`.
- Workaround: listed jobs per push through the Treeherder API and grepped ~90 raw logs (about 1.2 GB downloaded) to find the onset push.

## `fx-tests task` on a marionette job

- Command: `fx-tests task L01F0C9UReuP3nPBJtiANg --profiles`
- Expected: the job's test outcomes (268 passed, 1 failed per the log).
- Got: "0 tests, 0 executions ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one" — misleading: marionette's resource-usage profile has no test markers, but the job ran 268 tests. Pointing to `public/test_info/marionette_errorsummary.log` would answer it.

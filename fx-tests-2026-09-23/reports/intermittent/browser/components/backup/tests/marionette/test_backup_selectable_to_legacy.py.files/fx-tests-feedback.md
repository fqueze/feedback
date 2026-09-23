## `fx-tests task` reports "0 tests" for a marionette job whose resource profile has test markers

- Command: `fx-tests task PLE3PVYyR1uoMTnNiEbGVg --profiles` (test-linux2404-64/opt-marionette-integration-headless)
- Expected: the job's tests with their status, like for mochitest/xpcshell jobs. The resource-usage profile has 262 `test` markers, e.g. `FAIL — browser/components/backup/tests/marionette/test_backup_selectable_to_legacy.py BackupSelectableToLegacyTest.test_backup_selectable_to_legacy` with a `Test Name` field.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". A warning lists the 5 failures as "failing markers in this job named no test path". The marionette test name is `<path> <Class>.<method>`, so the path is the part before the space.
- Workaround: loaded the resource profile in profiler-cli and searched the `test` markers, and read the log.

## `fx-tests test` has no marionette data

- Command: `fx-tests test browser/components/backup/tests/marionette/test_backup_selectable_to_legacy.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". I could not get run counts or rates for a marionette test.
- Workaround: counted jobs per job type with Treeherder's `/api/project/<repo>/jobs/?job_type_name=...`, and used `fx-tests intermittent --bug` for the failures.

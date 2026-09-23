## `intermittent --bug` misses TEST-UNEXPECTED-ERROR lines, so "Tests named" hides the bug's own test

- Command: `fx-tests intermittent --bug 1723063 --since 30 --full-messages --limit 0`
- Question: which test failed in each annotated job?
- Expected: the 11 jobs where `test_crash.py` failed listed under "Tests named, per annotated job".
- Got: only 2 `test_crash.py` rows (the `TEST-UNEXPECTED-FAIL` ones) against 23 `test_marionette.py` rows; 9 occurrences had an empty `lines` array. Their `marionette_errorsummary.log` shows `test_crash.py TestCrashInTearDown.test_crash_in_teardown` / `TestCrashInSetUp.test_crash_in_setup` with status `ERROR` (a failure in setUp/tearDown logs `TEST-UNEXPECTED-ERROR`). So the summary made it look as if the bug were mostly about another test, and hid 9 of the 11 real occurrences.
- Workaround: downloaded `public/test_info/marionette_errorsummary.log` for every occurrence with no lines, and parsed it.
- What the output could have shown: a line for `TEST-UNEXPECTED-ERROR` like for `-FAIL`, or at least an "n occurrences with no failure line" count next to "Tests named".

## `task` on a marionette job says "0 tests"

- Command: `fx-tests task XpJL9oAvSNmADwIvrwlYng --profiles`
- Expected: the job's test outcomes (the resource-usage profile does have `test` markers with PASS/FAIL for each marionette test).
- Got: "0 tests, 0 executions, 0 failing" and "This profile records no tests at all", plus a warning that 2 failing markers "named no test path". The marionette marker names are `<path> <Class>.<method>`, so the path is there, followed by a space and the subtest.
- Workaround: loaded the resource-usage profile with profiler-cli and searched the `test` markers.

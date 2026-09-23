## `fx-tests task` reports "0 tests" for a marionette job (same as in test_backup_selectable_to_legacy.py.files)

- Command: `fx-tests task KCkiaV7JQF6V6BVnpDz3nQ --profiles` (test-linux2404-64/opt-marionette-integration-headless)
- Expected: the job's tests and their status. The resource-usage profile has `test` markers, e.g. `FAIL — browser/components/backup/tests/marionette/test_backup_selectable_to_selectable.py BackupSelectableToSelectableTest.test_backup_selectable_to_selectable`.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus a warning that the FAIL marker "named no test path".
- Workaround: `profiler-cli thread markers --search backup/tests/marionette --list` on the resource-usage profile, and the job log.

## Question: "how many jobs ran this marionette test this week, and how many failed it, per config?"

- Command: `fx-tests test browser/components/backup/tests/marionette/test_backup_selectable_to_selectable.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: listed the week's jobs per job type with Treeherder's `/api/project/<repo>/jobs/?job_type_name=...`, then fetched `public/test_info/marionette_errorsummary.log` for each of the 143 `testfailed` jobs and grepped for the test (found 12, two of them never annotated).
- What would have answered it: the same per-config runs/failures table `fx-tests test` gives for mochitest, from marionette errorsummaries.

## Question: "every annotated failure of this bug, on any tree"

- Command: `fx-tests intermittent --bug 2070602`
- Expected: the annotation count per tree, or a note that beta and try annotations exist and are left out.
- Got: "10 sheriff annotations on trunk, 2026-09-16 to 2026-09-22". Treeherder's `/api/failuresbybug/?bug=2070602&tree=all` has 22 in that window: 6 more on mozilla-beta (linux2404-64-shippable opt) and 6 on try. Nothing in the output says that other trees were left out.
- Workaround: `curl https://treeherder.mozilla.org/api/failuresbybug/?startday=...&endday=...&tree=all&bug=2070602`.

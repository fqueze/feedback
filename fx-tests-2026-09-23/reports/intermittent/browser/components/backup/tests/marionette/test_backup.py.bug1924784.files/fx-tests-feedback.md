# fx-tests feedback (test_backup.py.bug1924784)

## Question with no answer: which message did each job annotated on this bug fail with?

- Command: `fx-tests intermittent --bug 1924784`
- Got: "Tests named, per annotated job (none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a
  test — the API only keeps lines matching that marker)". Marionette reports this failure as
  `TEST-UNEXPECTED-ERROR | <path> <Class>.<method> | ...ScriptTimeoutException...`.
- Needed: the failure line of each occurrence (TEST-UNEXPECTED-ERROR included), to see that all 7 are
  `BackupTest.test_backup_disablement_in_new_session`, i.e. not the Wayland failure the bug is about.
- Workaround: downloaded the 7 `live_backing.log` files and grepped them.

## Question with no answer: is the same failure also starred on another bug?

- Command: `fx-tests intermittent --bug 1924784`
- Needed: the other bugs whose annotated jobs fail with the same message. The same
  `test_backup_disablement_in_new_session` ScriptTimeout is starred on bug 2052509 (12 jobs) and on
  bug 1924784 (7 jobs) in the same week; each bug alone undercounts it by a third to two thirds.
- Workaround: listed all tsan/debug marionette-integration jobs of the week through the Treeherder REST API
  and grepped the 38 failing logs.

## `fx-tests task` says a marionette job's profile records no tests

- Command: `fx-tests task BUeN6DXwRMGikL8gOofHKw --profiles`
- Expected: the job's tests (the resource-usage profile has `test` markers such as
  `ERROR — browser/components/backup/tests/marionette/test_backup.py BackupTest.test_backup_disablement_in_new_session`).
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all". Same issue as logged
  in `test_backup.py.bug2052509.files/fx-tests-feedback.md`.
- Workaround: loaded the profile with profiler-cli and listed `--search test_backup.py`.

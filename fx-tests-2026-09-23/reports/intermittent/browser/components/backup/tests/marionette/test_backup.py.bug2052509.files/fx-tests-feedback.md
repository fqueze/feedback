# fx-tests feedback (test_backup.py.bug2052509)

## `fx-tests task` says a marionette job's profile records no tests, but it has `test` markers

- Command: `fx-tests task e-tfLXsOQO-0OQDuekI4Dg --profiles`
- Expected: the job's tests with their status (the resource-usage profile has 16 `test` markers, e.g.
  `ERROR — browser/components/backup/tests/marionette/test_backup.py BackupTest.test_backup_disablement_in_new_session`,
  and `PASS — ... BackupTest.test_backup`).
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a
  test job, or the harness died before it ran one", plus a warning that the ERROR marker "named no test path"
  (it does name one: the path is followed by a space and the unittest method name).
- Workaround: loaded the resource-usage profile with profiler-cli and listed `--search test_backup.py`.

## Question with no answer: how often does a marionette test run and fail, per config?

- Command: `fx-tests test browser/components/backup/tests/marionette/test_backup.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only takes
  xpcshell|mochitest.
- Needed: runs and failures per config for a marionette test, and a few passing task IDs to compare with.
- Workaround: Treeherder REST `jobs/?task_id=` then `jobs/<id>/similar_jobs/` in a Python script to count
  pushes per config and pick passing jobs, then grepped their logs.

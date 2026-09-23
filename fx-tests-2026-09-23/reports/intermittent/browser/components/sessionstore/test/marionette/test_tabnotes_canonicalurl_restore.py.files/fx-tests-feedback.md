## Marionette tests are invisible to `fx-tests test` (2026-09-22, test_tabnotes_canonicalurl_restore.py)

- Command: `fx-tests test browser/components/sessionstore/test/marionette/test_tabnotes_canonicalurl_restore.py`
- Expected: runs/failures per config and `--history` for a marionette test, the way `fx-tests intermittent --bug 2022502` already knows about its annotated failures.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — no rate, no history, no task list.
- Workaround (cost: ~30 min and ~3,500 Treeherder/Taskcluster requests): list every marionette-integration job via Treeherder `api/project/<repo>/jobs/?job_type_name=<exact name>&last_modified__gte=...` (the per-push `api/jobs/?push_id=` endpoint silently truncates at 2000 jobs, and autoland pushes have more), download each job's `public/test_info/marionette_errorsummary.log`, count jobs whose `groups` include the manifest and those whose failures carry the message. The question this answered: "how often does this marionette test fail per config, per day, and did it stop?".

## `fx-tests task` on a marionette job says "0 tests" although its resource profile has per-test markers

- Command: `fx-tests task bYowV5_8Qm20IZu0Cv0xOA --profiles`
- Expected: the failing test listed with its status, as for mochitest jobs.
- Got: "0 tests, 0 executions, 0 failing" plus a warning that a failing marker "named no test path". The resource-usage profile does hold `test` markers for each marionette test (`FAIL — browser/.../test_tabnotes_canonicalurl_restore.py TestCanonicalUrlRestore.test_canonicalUrl_cleared_when_feature_disabled`): the name is `<path> <Class>.<method>`, which the parser apparently does not split into path + subtest.
- Workaround: read the job log and the resource profile directly.

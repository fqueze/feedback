## `intermittent --bug` on a non-test (lint) bug: "since when, and has this job passed since?"

- Command: `fx-tests intermittent --bug 2068807`
- Question: when did this lint job (source-test-mozlint-eslint-build, a daily central cron) start failing, and has any run passed since?
- Expected: the job's pass/fail runs per day, or at least all annotations since the bug was filed (it was filed 2026-09-03).
- Got: the 6 annotations from the last 7 days only. `fx-tests test` does not apply, since there is no test path.
- Workaround: Treeherder API `api/project/mozilla-central/jobs/?job_type_name=source-test-mozlint-eslint-build&last_modified__gt=...` (22/22 testfailed since the 09-03 cron, success through 09-02) and `api/failuresbybug/?bug=2068807`, which also showed 6 unrelated snap jobs mis-starred on 09-02.
- Could have shown: for a bug whose occurrences all come from one job name, that job's run history over the window, with pass/fail, and a flag on occurrences from other job names.

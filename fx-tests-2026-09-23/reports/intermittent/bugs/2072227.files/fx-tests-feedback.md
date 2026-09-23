## Question: "on which workers did this bug's occurrences run?"

- Command: `fx-tests intermittent --bug 2072227`
- The occurrences table has push time, tree, platform, build, job name, task id, but no machine name. For an infra bustage (here: disk full on two specific macOS hardware builders) the worker is the discriminating dimension, and the answer was not there.
- Treeherder's `/api/failuresbybug/?bug=N&startday=...&endday=...&tree=all` returns `machine_name` per occurrence. It also returned 25 occurrences since 2026-09-15 against 8 from `fx-tests` (window starts 2026-09-16), which is the documented window, not a bug.
- Workaround: the Treeherder API plus Taskcluster `task/<id>/status` for `workerId` of every run, including the passing retries, and Treeherder `jobs/?job_type_name=...` for the passing runs on each worker.

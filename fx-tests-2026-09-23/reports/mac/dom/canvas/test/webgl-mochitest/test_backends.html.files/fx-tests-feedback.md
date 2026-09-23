## Question: which worker pool / which kind of push ran the failing jobs?

- Command: `fx-tests test dom/canvas/test/webgl-mochitest/test_backends.html --task-ids --limit 0 --issue 2`
- Expected: some sign that 21 of the 22 failing jobs ran on `gecko-t/win11-64-24h2-gpu-alpha`, from os-integration cron task groups (`createdForUser: cron@noreply.mozilla.org`), rather than on the pool the config normally uses (`win11-64-25h2-gpu`). Ideally a worker-pool column in `--task-ids`, or os-integration/alpha runs flagged or split out of the per-config rates.
- Got: task IDs, configs and dates only. The per-config table puts these jobs in `test-windows11-64-25h2/opt-mochitest-webgl1-core` (5.8%), and nothing shows that the rate comes from an image-validation pool.
- Workaround: one Taskcluster `queue/v1/task/<id>` and `/status` call per task for `taskQueueId`, `workerId` and `tags.createdForUser`, then `task-group/<id>/list` to check that every alpha-pool webgl1-core job failed. This was the key finding, and it took about 6 scripted calls.

## `fx-tests test <path> --bugs` printed no bug section

- Command: `fx-tests test dom/canvas/test/webgl-mochitest/test_backends.html --bugs`
- Expected: the bugs naming the test, or an explicit "no bugs found". Six bugs name it, all closed; the tracking bug 1777064 was closed INCOMPLETE on 2026-08-24.
- Got: the same output as without `--bugs`, with no bug section at all, so "none" could not be told apart from "the flag did nothing".
- Workaround: a Bugzilla REST query on `summary=test_backends.html`.

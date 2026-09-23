## Question: the task IDs of the passing jobs on a push, to compare a failing job with passing ones of the same config

- Command: `fx-tests try af7de80ceb4e68b527438cb03aa27193c978a5ca --project autoland --all-jobs --test toolkit/mozapps/update/tests/unit_background_update/test_backgroundupdate_exitcodes.js --task-ids` (also with `--json`)
- Expected: next to each config's "passed" count, the task ID of the job, so its resource-usage profile can be loaded.
- Got: per-config counts only (jobs/passed/passed on retry/failed); `--task-ids` adds nothing, and the JSON has no task IDs either. It read every job's profile to produce the counts, so it had them.
- Workaround: listed the push's Taskcluster task group with the queue API and filtered on the job name.

## Question: which test in this job never finished its first run (the leaker)

- Command: `fx-tests task KhA8GlGQQoiIISJEmOboOQ`
- Expected: `toolkit/crashreporter/test/unit/test_crashreporter_crash.js` to appear somehow: its first run in the parallel phase timed out (its per-test timeout profile was uploaded) and never logged a `test_end`; the resource-usage profile has `ERROR test_start for toolkit/crashreporter/test/unit/test_crashreporter_crash.js logged while in progress.` at its retry.
- Got: the test is absent from the FAILED list (its retry passed, and the first run has no outcome). In 21 failing jobs of test_backgroundupdate_exitcodes.js this never-ended run was the cause, and `task` hid it in about half of them (the other half list it as CRASH).
- Workaround: loaded each resource-usage profile and searched markers for `logged while in progress`. A "never ended" section in `task` (or a flag on the row) would have answered it directly.

## review-test_backgroundupdate_exitcodes.js — Question: in the jobs where test A failed, did test B fail or pass?

- Commands: `fx-tests test toolkit/crashreporter/test/unit/test_crash_terminator.js --task-ids --limit 0`, the same for this test, then `fx-tests task <id> --passed --limit 0` per job.
- Expected: one way to cross two tests over the same jobs, such as `fx-tests test <A> --with <B>`: the jobs where A failed, each with B's outcome (failed, passed, not in this chunk).
- Got: two separate task-ID lists. Joining them took a shell script, and telling "B passed" from "B not in this job" took one `task --passed` call per job, because `--task-ids` lists only failing jobs.
- Workaround: `grep -qx` over the two lists, plus per-job `task --passed`. That found 400 macOS crash_terminator timeouts against 13 exitcodes failures, which is what led to the survivor/no-survivor split.

## Question: "where are the minidumps of this test's force-killed runs?"

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_aus_update/languagePackUpdates.js --task-ids --limit 0 --json` (and `fx-tests task eQNHEeY-RKyVLsoTCp3ueQ --messages`)
- Expected: minidump IDs for the debug jobs whose status is `CRASH, TIMEOUT` (the harness force-kills xpcshell and uploads a dump), since the guide says `--task-ids` gives "where the dump was uploaded, a minidump ID".
- Got: `crashSignatures: []`, no minidump ID anywhere; `task --messages` shows only the crash frame `@ __psynch_cvwait`.
- Workaround: listed the task's artifacts through the Taskcluster queue API to find `public/test_info/<uuid>.dmp`, then `fx-tests crash <task> <uuid> --all-threads`, which worked well and was decisive (it showed the `UpdateProcessor` thread in `-[NSConcreteTask waitUntilExit]`).
- What the output could have shown: the dump IDs next to each CRASH execution in `task --messages`, and a pointer to `fx-tests crash`.

## Question: "which workers ran the failing jobs?"

- Command: `fx-tests test <path> --task-ids --limit 0 --json`, then a Python loop over the Taskcluster `task/<id>/status` endpoint for `workerId`.
- Expected: a worker column (or a `--by-worker` count) for the failing jobs, to tell a few bad machines from a fleet-wide problem.
- Got: task IDs only.
- Answer here: 135 failing jobs spread over 41 `macmini-m4-*` workers, so it is not a handful of broken machines.

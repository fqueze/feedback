# fx-tests feedback (browser_last_window_close_trigger.js)

## Question: which minidump is the hung parent process, and what was its main thread doing?

- Command: `fx-tests test browser/components/tests/browser/browser_last_window_close_trigger.js --task-ids --limit 0`
- Expected: `--help` says it prints "the minidump IDs of any crashes". The 4 timeout jobs are all `CRASH, TIMEOUT` (the harness kills the hung browser and writes dumps), and each task has 7-9 `public/test_info/<uuid>.dmp` + `.json` artifacts.
- Got: task IDs only, no minidump ID.
- Workaround: listed artifacts with `curl .../api/queue/v1/task/<id>/runs/0/artifacts`, then looped `fx-tests crash <task> <uuid> --thread 0` over every dump to find the parent-process one (the rest are child processes idling in `XRE_InitChildProcess`). About 10 calls per task.
- What would have answered it: the minidump IDs per failing test, with the process type (or "main thread in XRE_main" vs "XRE_InitChildProcess") so the parent dump stands out. For a harness-killed hang, the parent's main-thread top frames are the diagnosis.

## `task` counts one hung execution as "10 failing executions of 10"

- Command: `fx-tests task O2OKFBENRJ-Y0bH32depyA --profiles`
- Expected: 1 failing execution (the test ran once and hung; standalone job, no rerun).
- Got: `CRASH, TIMEOUT — 10 failing executions of 10`, and `5 of 5` / `2 of 2` / `3 of 3` in the other jobs: the kill dumps are counted as executions.
- Workaround: read the resource-usage profile, which shows one `test` marker plus N `CRASH` markers.

## `test --bugs` with no bug prints nothing to say so

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none found" line.
- Got: the same output as without `--bugs`; no way to tell "searched, none" from "flag ignored".

## (review) `crash` with a short minidump ID says the artifact expired

- Command: `fx-tests crash O2OKFBENRJ-Y0bH32depyA 8ad80c47 --thread 0 --frames 0`
- Expected: a match on the ID prefix (reports quote the first 8 hex digits), or "no minidump with that ID; pass the full UUID".
- Got: exit 4, `the artifact is not there. Taskcluster expires artifacts, so a dump from an old task is permanently gone — retrying will not help.` The task is 8 days old and its other dumps can still be fetched, so that message is misleading.
- Workaround: none without the full UUID. It is in the `minidump` field of the resource-usage profile's `CRASH` markers.

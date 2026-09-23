## `fx-tests try --test` ignores `--task-ids`

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/base/content/test/browser-webrtc/browser_devices_get_user_media_queue_request.js --task-ids` (also with `--json`)
- Expected: the task IDs of the jobs behind each count, in particular the one "passed on retry" job on debug-mochitest-browser-chrome-6.
- Got: only the per-config counts table; no task IDs in text or JSON output.
- Workaround: re-ran `fx-tests try <rev> --all-jobs --task-ids --profiles --limit 0` (whole push, ~3000 lines) and grepped for the test name. `--config` is refused on `try`, so that could not be narrowed either.
- Question it should answer: "which task(s) of this push ran / failed this one test".

## Task IDs of the passing jobs that ran a test

- Question: "which tasks of this push ran this test and passed", to compare a passing run's job timeline with the failing one.
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test <path>` gives only counts per config (opt-mochitest-browser-chrome-2: 2 passed, ...), and `--all-jobs --task-ids` without `--test` lists task IDs only for failures.
- Workaround: listed the task group through the Taskcluster queue API (`task-group/<id>/list`) and filtered by job name in a Python script.
- What could show it: `--test ... --task-ids` printing each job's task ID and outcome under its config row.

## Which jobs ran this test and passed, so I can compare their logs?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_addons_manifest_url.js --task-ids` (also with `--json`).
- Expected: the task IDs behind each count (the passed jobs in particular), since `--task-ids` was given.
- Got: only per-config counts (`debug-mochitest-devtools-chrome-4: 2 jobs, 1 passed, 1 failed`; `opt-mochitest-devtools-chrome-2: 2 jobs, 2 passed`); no task IDs in text or JSON.
- Workaround: Treeherder `api/project/try/jobs/?push_id=...` filtered by job name, then matched against the failing task.
- What the output could have shown: one task ID (and worker name) per job row. The comparison with the passing jobs' logs (adb printing "daemon started successfully") was the key evidence here.

## Every failure line this job logged for this test

- Command: `fx-tests task CpHh9VmXQ9KMzYSzxG30oQ --messages --full-messages`.
- Expected: all failure lines for the test, including `TEST-UNEXPECTED-TIMEOUT | ...addons_manifest_url.js | application timed out after 370.0 seconds with no output` (twice in this job's log) and the attributed `TEST-UNEXPECTED-FAIL | leakcheck | default 352 bytes leaked (Mutex, StringBuffer, nsLocalFile, nsProcess)` (three times).
- Got: only `Test timed out`, `leaked 1 window(s) until shutdown [url = about:debugging]` and `leaked 1 docShell(s) until shutdown`.
- Workaround: downloaded `public/logs/live_backing.log` and grepped it.
- What the output could have shown: the no-output timeout and the leakcheck lines, which here proved the spawned child outlived Firefox.

## What does "2/3" count on the NEW INTERMITTENTS row?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`.
- Got: `browser_aboutdebugging_addons_manifest_url.js  2/3`, while `--all-jobs --test` says 4 jobs ran it (1 failing job with 2 failing executions, 3 passing jobs).
- Expected: a denominator that says what it counts (executions in failed jobs read? jobs?).

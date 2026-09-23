## The failing tasks of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/base/content/test/browser-contextMenu/browser_contextmenu.js --task-ids` (also with `--json`)
- Expected: the per-config table, plus the task IDs (and profile URLs with `--profiles`) of the job runs where the test failed, since `--task-ids` was accepted.
- Got: the per-config table only (`debug-mochitest-browser-chrome-4: 2 jobs, 0 passed, 2 failed`); `--task-ids` silently ignored, and the JSON has no task IDs either.
- Workaround: rerun `fx-tests try <rev> --profiles --task-ids --limit 0` (the default output had hidden the row among "… 41 more" perma-fails) and grep for the test path.
- What the output could have shown: under each config row with failures, the `task <id>.<run>` lines and the failure's first message, as the default `try` rows do.

## The minidump behind a CRASH row

- Command: `fx-tests task evZeydo9QsOSkHu39igTeg --profiles` (and `--json`)
- Expected: for the CRASH row `@ mozilla::AppWindow::FullscreenWillChange`, the minidump ID that `fx-tests crash <taskId> <minidumpId>` needs.
- Got: the signature only; the JSON's `failures[0]` carries only `path` and `messages`.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<taskId>/runs/0/artifacts` and pick the `public/test_info/<uuid>.dmp` name.
- What the output could have shown: `dump <uuid>` on the CRASH row, and a note that crashed tests leave no per-test profile.

# fx-tests feedback

## Question: which try jobs ran this test and passed, so I can read their resource-usage profile?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_connect_toggle_usb_devices.js --task-ids` (and the same with `--json`)
- Expected: the task IDs behind each count (passed / failed), as `--task-ids` promises elsewhere.
- Got: per-config counts only (`debug-mochitest-devtools-chrome-4: 2 jobs, 1 passed, 1 failed`; `opt-mochitest-devtools-chrome-2: 2 passed`); `--task-ids` is silently ignored, and the JSON has no task IDs either.
- Workaround: none found cheaply; the passing runs were not read.

## Question: how many times did this test run on the push?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0` (with and without `--all-jobs`)
- Expected: the NEW INTERMITTENTS row to count the runs of the test on the push.
- Got: `1/2` for the test, while `--test` with `--all-jobs` shows it ran in 4 jobs (1 failed, 3 passed). The `2` is not explained anywhere.
- Also: the row for this test was hidden behind `… 1 more (--limit 0 for all)` in the default output, which is easy to miss when grepping for the test.

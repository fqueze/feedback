## Question: which task is the "passed on retry" run of my test on this try push?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/components/customizableui/test/browser_995164_registerArea_during_customize_mode.js --task-ids --profiles` (also with `--json`)
- Expected: the task ID (and per-test failure profile URL) behind the "passed on retry" count for `opt-mochitest-browser-chrome-no-nv-5`.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either. The default `fx-tests try` listing (failed jobs only) did not list the test at all, since the job was green.
- Workaround: queried the Treeherder jobs API for the push (`/api/jobs/?push_id=...`) to get the two task IDs of that config, then `fx-tests task` on the green one.
- What the output could have shown: the task ID(s) per row, and the failure profile URL for "passed on retry" / "failed" rows.

## Question: "a job, on the failing config, where this test passed on its first run"

- Needed a passing first run (in manifest order, not the harness retry) to compare against the failing runs' logs.
- Command tried: `fx-tests test browser/components/urlbar/tests/browser-UrlbarInput/browser_setURI.js --task-ids --limit 0` — lists only failing tasks; `--coverage` gives pass counts per config but no task IDs.
- Expected: some way to list task IDs where the test ran and passed on a config (e.g. `--task-ids --status pass --config <cfg> --limit 5`).
- Got: no such option.
- Workaround (costly): took the failing-task list of a test often co-chunked on the same config (`browser_policy_sitepolicies_jit.js`), subtracted this test's failing tasks, then ran `fx-tests task <id> --json` on each candidate and grepped for the test path to find jobs that ran it (2 of 12 did). A script over `--json` was needed because the default output of `fx-tests task` does not list passing tests.

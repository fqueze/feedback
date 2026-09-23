## "How many runs failed with message X" has no answer when X is not the first message of the run

- Question: how many of this test's failing runs are `This test exceeded the timeout threshold` (bug 2063342)?
- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js --task-ids --limit 0 --json`
- Expected: a way to count runs by any message they logged, or at least by their first *unexpected* one.
- Got: `taskIds[].message` holds only the first message, here the `TEST-KNOWN-FAIL` a11y todo (645 runs). The timeout-threshold message appears nowhere in `test`'s output, not even in `messages`. `fx-tests task <id> --messages` shows it, but only one job at a time.
- Workaround: counted the runs under the a11y-todo row per config with a script over `--json`, then confirmed in 3 profiles that the timeout threshold was the real failure behind them. Could show: per-message run counts across all of a run's unexpected messages, or skip TEST-KNOWN-FAIL when picking the "first failure".

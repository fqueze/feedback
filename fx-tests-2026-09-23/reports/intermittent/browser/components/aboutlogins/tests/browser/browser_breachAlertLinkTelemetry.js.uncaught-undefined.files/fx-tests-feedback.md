## Question: which configs, and which days, one failure mode of a test hits

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js --task-ids --issue 2 --limit 0`
- Expected: the per-config table (and ideally the per-day History) restricted to issue 2, since this test has two modes of similar size (754 vs 673) with very different platform spreads (Windows opt vs Linux sanitizer/debug).
- Got: the per-config table and History for all modes, then a flat per-day list of task IDs for issue 2.
- Workaround: awk over the task list, stripping the chunk number from each job name and counting per config and per date header. Counts jobs, not runs, and gives no pass denominator, so no per-config rate for the mode.

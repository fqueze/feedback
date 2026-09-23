## Question: per-day history of one failure mode (did mode 1 or mode 2 cause the step change?)

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js --history --issue 1`
- Expected: the History table restricted to runs whose first failure is issue 1, so a step change can be attributed to one failure mode when a test has two of similar size (754 vs 673 here).
- Got: exit 1, "--issue ... needs --task-ids".
- Workaround: `--task-ids --issue N --limit 0` and awk-counting the task lines under each date header. Works, but counts jobs rather than runs, and has no pass denominator.

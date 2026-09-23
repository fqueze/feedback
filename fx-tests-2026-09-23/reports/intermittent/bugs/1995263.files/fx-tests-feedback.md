## Question: which occurrences of a bug are the minority job names, and when?

- Command: `fx-tests intermittent --bug 1995263 --since 60 --history`
- Expected: a way to see the 10 annotations whose job was not `web-platform-tests-webgpu-long` (dates, platforms, task ids), since the "Job names" summary lists them (8x geckoview-test-verify-nofis, 1x geckoview-xpcshell, 1x web-platform-tests-webgpu).
- Got: the Occurrences table truncates the job name to `web-platfor…` and the platform to `windows11-6…`, so rows cannot be told apart; no `--config`-style filter on the drill-down worked for this.
- Workaround: `--json`, then a script over `occurrenceRows` filtering on `testSuite`.
- Could have shown: untruncated job name in the occurrence rows (or `--full-messages` applying to them), or `--config <substring>` narrowing the drill-down's occurrences.

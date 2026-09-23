## `test --task-ids` does not say which failure mode each task had, nor split modes by config

- Command: `fx-tests test browser/components/shell/test/unit/test_dynamicLauncher.js --task-ids --limit 0`
- Expected: next to each task, which of the Issues it had (here TIMEOUT vs "Invalid process ID: N"), or a per-config breakdown of the Issues.
- Got: a flat list of tasks per day with config only; the Issues list has counts but no config. Finding that all 117 TIMEOUTs are ccov and all 246 "Invalid process ID" are debug took one `--issue <n>` run per mode plus `awk | sort | uniq -c` over the output.
- Also: "Invalid process ID: 6 / 18 / 27 / 34" are one failure mode split into four Issues by the number in the message; normalizing digits (as for other messages) would show it as one 246x issue.

## Question: when did the TIMEOUT tests of this job start, and how long did they last?

- Command: `profiler-cli thread markers --session test_breakpoint-10.js-1 --search "Status:TIMEOUT"` (aggregate), then `--list --limit 0 --json` + a Python script.
- Expected: the aggregate view to give, per marker name, the start-time range (first/last start) alongside the duration stats, so "477 `test` markers, started 36.99-48.75 s, durations 45.00-45.07 s except 7" reads directly.
- Got: the aggregate gives count and duration min/avg/max, but no start-time range; `--list` gives 477 rows to eyeball. Needed a script over `--json` to split the 7 that started before a given time from the 470 after it.

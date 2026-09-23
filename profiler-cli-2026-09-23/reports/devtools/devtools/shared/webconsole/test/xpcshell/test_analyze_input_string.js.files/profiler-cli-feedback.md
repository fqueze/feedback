## Question: what was the machine's CPU use over a time range, in a resource-usage profile?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session test_analyze_input_string.js-1` (after `zoom push 150.5,151.8`)
- Expected: each row, or a summary line, giving the `cpuPercent` (and user/system split) of the interval, or a range average.
- Got: rows with name, start and duration only; the values are only in `marker info` or `--json`. `profile info` on the same profile says "CPU activity over time: No significant activity", since the only thread has no samples and the CPU lives in markers.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` weighted by duration, per 10 s bucket.
- What could have shown it: `profile info` / `counter info`-style "machine CPU over time" built from `CPU Use` markers when a profile has no samples, or the payload's first field in `--list` rows.

## Question: what is the fastest passing test in this job (duration distribution of `test` markers by status)?

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --session test_analyze_input_string.js-1`
- Expected: a way to sort by duration, or min/max per status.
- Got: chronological rows only; `--group-by field:status` would group, but the per-group min is only in JSON `durationStats`. (`--search status:PASS --max-duration 700 --list` would have worked; I found `--max-duration` only afterwards in `--help`.)
- Workaround: `--json` and a script sorting PASS durations (fastest 689 ms, against the 46 ms FAIL).

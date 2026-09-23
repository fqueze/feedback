## Question: "was the machine saturated between t1 and t2?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --list --search "name:CPU Use"` after `zoom push 1452,1482`
- Expected: the CPU percent of each `CPU Use` marker in the list row, or better a summary (mean/max CPU% over the zoomed range, or per-N-second buckets).
- Got: rows with only time and duration; the percentage is only in `marker info` (one marker at a time) or `--json`.
- Workaround: `--json` over the whole profile (24k markers) and a Python script bucketing `cpuPercent` by 10 s.
- What would have answered it: a `CPU Use` value column in `--list`, or `counter`-like stats for the resource-usage CPU markers over the current zoom.

## Question: "which tests' child processes hung at shutdown, and how long after each test started?"

- Command: `profiler-cli thread markers --session <s> --list --search "may be hanging at shutdown"`
- Expected: the `test` field of each C++ warning shown in the list row (the marker carries it: `--search <test name>` matches it).
- Got: message and source location only; the test name needs `--json`.
- Workaround: `--json` for the warnings and for all `test` markers, joined by test name in a script.

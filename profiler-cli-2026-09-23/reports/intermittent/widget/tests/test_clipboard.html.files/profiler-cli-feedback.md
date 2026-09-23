## Question: what was host CPU use during one test's interval?

- Command: `profiler-cli zoom push m-1` (the `test` marker), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: each CPU Use marker's `CPU Percent`, or better an average over the zoomed range.
- Got: handles and durations only. I needed `profiler-cli marker info m-131..m-148 | grep "CPU Percent" | awk` to get the average, and repeated that for each comparison run.
- What could answer it: a CPU-percent column in `--list` for `CPU` markers, or a range summary (mean/max CPU Percent) in `thread markers` when the payload field is numeric.

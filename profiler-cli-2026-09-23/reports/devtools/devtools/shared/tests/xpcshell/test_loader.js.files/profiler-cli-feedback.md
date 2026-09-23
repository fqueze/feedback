## Question: "was the machine idle while these tests timed out?"
- Command: `profiler-cli thread markers --session test_loader.js-1 --search "name:CPU Use" --list` (resource-usage profile, xpcshell job)
- Expected: the CPU percentage on each row.
- Got: rows with only name, time and duration; the percentage needs `marker info` per marker. `counter list` says "No counters in this profile".
- Workaround: `marker info` on a few hand-picked markers.

## Question: "when did the TIMEOUT tests start, as a distribution?"
- Command: `profiler-cli thread markers --search name:test --list --limit 0` then grep/awk over 1742 rows.
- What would have answered it: `--group-by field:status` with start-time min/max per group, or a histogram of start times for a filtered set.

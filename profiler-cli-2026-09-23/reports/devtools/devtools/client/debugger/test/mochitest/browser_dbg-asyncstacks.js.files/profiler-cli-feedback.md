## Question: when did a repeating marker become continuous (and did it ever stop)?

- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --session <s>` (then `--list --limit 0 --json` piped to a script).
- Expected: something that says the marker fires on every vsync from 31.103 s to the profile end, i.e. the start and end of the dense runs.
- Got: the aggregate gives count and min/avg/max interval (avg 17 ms, max 37 ms in the zoom), which does not say where the run starts; `--list` gives 437 rows.
- Workaround: script over `--json` clustering starts with a 200 ms gap. A "runs" / "bursts" summary (start, end, count per run of a marker name, with a gap threshold) would have answered it.

## Question: average of a numeric marker field over a range (machine CPU in the resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --json --list --limit 0 --session <resource profile>`, then a script averaging `data.cpuPercent` over the test's phases.
- Expected: the average/max `cpuPercent` of the `CPU Use` markers inside a zoom, e.g. in the `thread markers` aggregate for that name.
- Got: only counts and durations; the value is a string ("26.4%") in the JSON, so even the script has to parse it.
- Workaround: script. A per-field min/avg/max for numeric payload fields in the aggregate view would answer "was the machine saturated during X" directly.

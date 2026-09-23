## Question: what was the machine's CPU use while this test ran? (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session <s>` (the `test` marker) then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- Expected: each `CPU Use` row showing its CPU Percent (or a summary: min/avg/max CPU % over the zoomed range).
- Got: 40 rows with only handle, time and duration (`m-5653  CPU Use  t=2m21s  103ms`), no value; `profile info` and `counter list` say "No significant activity" / "No counters".
- Workaround: `--json` and a Python loop over `flatMarkers[].data.cpuPercent`. A summary line for CPU Use markers in the zoomed range (or the percentage in the `--list` label) would have answered it directly.

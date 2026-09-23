## Question: "was the machine CPU-saturated during this time window?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use"` (with and without `zoom push 291,312`).
- Expected: something answering "CPU% over time" — e.g. min/avg/max of the `cpuPercent` field over the zoomed range, or a per-bucket timeline like `counter info`'s "over time" section.
- Got: interval-duration stats only (min/avg/max marker duration), not the CPU values. `counter list` says "No counters in this profile", because the resource-usage profile carries CPU as markers.
- Workaround: `--list --limit 0 --json` piped into a Python script that buckets `data.cpuPercent` per 5 s / per minute. The same script also bucketed "may be hanging at shutdown" markers per minute, which a `--group-by time:<bucket>` on `thread markers` would have answered directly.

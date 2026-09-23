## Was the machine saturated during this window? (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --session <s>` on `profile_resource-usage.json` (task HzPyFu9jRrKg44gEJ5QTkA), zoomed to the stall.
- Expected: the CPU % of each `CPU Use` marker, or a per-second CPU summary of the zoomed range.
- Got: one row per marker with name/time/duration only; the cpuPercent/idle_pct payload is not printed. `counter list` says "No counters in this profile" and `profile info` says "CPU activity over time: No significant activity", which reads as "the machine was idle".
- Workaround: `--list --limit 0 --json` piped through a Python script that averages `data.cpuPercent` per second.

## When was this process alive? (per-test profile)
- Command: `profiler-cli profile info --all --session <s>`
- Expected: process start/end in seconds, e.g. `[41.788s → end]`, to line processes up with the test's markers.
- Got: `[ts-tr → end]`, `[ts-G1 → ts-t]`: timestamp handles, not times.
- Workaround: `profile info --all --json` and read `startTime`/`endTime`.

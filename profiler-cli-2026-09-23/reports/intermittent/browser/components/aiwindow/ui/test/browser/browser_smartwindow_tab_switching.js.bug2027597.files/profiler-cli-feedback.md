## Machine CPU use over a time range of a resource-usage profile

- Question: "what was the machine's average CPU / idle % between t=18m55s and t=24m55s" (was a timed-out job stalled or saturated?).
- Command: `profiler-cli zoom push 1135,1495 --session S; profiler-cli counter list --session S` -> "No counters in this profile."; `profiler-cli thread markers --session S --search "name:CPU Use" --list` lists 3,601 one-row markers without their values.
- Expected: an aggregate of the `CPU Use` markers' `cpuPercent` / `idle_pct` fields over the zoom (mean, max), or those fields as columns in `--list`.
- Workaround: `--list --limit 0 --json` and a Python one-liner averaging `data.idle_pct` (mean idle 98.0%).

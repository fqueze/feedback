## Question: what was the machine's CPU use, second by second, over a time range of a resource-usage profile?

- Command: `profiler-cli zoom push 213.8,227.2 --session S` then `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` (also tried `--json`).
- Expected: the CPU percent of each `CPU Use` marker, or a per-second summary of it, in the list (or `counter info` on a CPU track).
- Got: only the name, start and duration per row; `--json` has the same fields, no payload. `counter list` says "No counters in this profile".
- Workaround: took the handles from `--list --json`, passed them all to `marker info ... --json`, and averaged `cpuPercent` per second in a python script.
- What would answer it: a `CPU %` column for CPU-type markers in `--list`, or exposing the resource-usage CPU markers as a counter so `counter info` gives the over-time buckets.


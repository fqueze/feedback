## Machine CPU use over a time range needs a script

- Question: was the machine busy or idle while the test hung (from t=74.5s to t=164.1s in a resource-usage profile)?
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push m-36`
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean, min, max, maybe per-N-second buckets), the way `counter info` does for counters.
- Got: 897 rows with name and duration only; the CPU values are not in the text output. `counter list` says the profile has no counters, so CPU use exists only as marker payloads.
- Workaround: `--json` and a Python script that averaged `data.cpuPercent` over 5 s buckets.

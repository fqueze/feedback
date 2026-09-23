## Question: "how busy was the machine between t1 and t2?" (resource-usage profile)

- Command: `profiler-cli zoom push 300,325 --session <s>` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- Expected: the CPU percentage per marker (or a summary: mean/min/max CPU % over the zoomed range).
- Got: one row per CPU Use marker with only its time and duration; the percentage needs `marker info` per marker. `profile info` says "No significant activity" and `counter list` has no counters for these profiles, so there is no CPU track to query either.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per bucket.
- Could have shown: the `CPU Percent` field in the list rows, or a `--stats field:cpuPercent` style aggregate over the filtered markers.

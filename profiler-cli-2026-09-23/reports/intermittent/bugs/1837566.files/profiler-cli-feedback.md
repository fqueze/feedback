## Machine CPU over a window of a resource-usage profile

- Question: "how busy was the machine in the seconds around t=432.9s of this resource-usage profile?"
- Command: `profiler-cli thread markers --session 1837566-1 --search "name:CPU Use" --list --limit 0` after `zoom push 429.5,434`
- Expected: each `CPU Use` row showing its `cpuPercent` (and `idle_pct`), like other markers show their payload text.
- Got: rows with only name, time and duration (`m-137 CPU Use t=7m8s 94ms ✗`), no value; `thread markers` without `--list` gives no stats over the payload either.
- Workaround: `--json` and a Python loop over `flatMarkers[].fields` to print `cpuPercent` per marker. Same for `Memory` (`used`).

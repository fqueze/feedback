## Question: "was the machine busy while this test waited?" in a resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>`
- Expected: the CPU % of each 100 ms sample in the list rows, or a counter (`counter list` says "No counters in this profile").
- Got: rows with no value; the percentage is only in `marker info` per marker, so reading 25 samples needed `--json` and a Python one-liner over `.flatMarkers[].data.cpuPercent`.
- What would have answered it: the marker's CPU Percent field in the `--list` row description, or `counter info`-style over-time buckets for CPU Use markers.

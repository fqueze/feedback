## Machine CPU over one test's duration, in a resource-usage profile
- Question: "was the machine busy while this test hung?"
- Commands: `profiler-cli profile info` ("No significant activity"), `profiler-cli counter list` ("No counters in this profile"), `profiler-cli thread markers --search "CPU Use" --list` (rows with no values).
- Expected: the CPU Percent of each `CPU Use` marker in the list rows, or a CPU summary in `profile info` / `counter list` built from those markers.
- Workaround: `--json`, then `flatMarkers[].fields[key=cpuPercent]` in a script.

## Finding the marker a link's `marker=N` points to (review)
- Question: "is `marker=95461` in this link the `CPU Use` marker the report quotes?"
- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --json` (inside a zoom), then `marker info <m-…> --json` per handle.
- Expected: the marker index in the `--list` rows or its `--json`, or a way to look a marker up by index (`marker info --index 95461`).
- Got: the rows and their `--json` carry handles only (keys: handle, name, label, start, duration, hasStack, category, markerType, fields, data). For 28 markers with identical names, finding index 95461 took a shell loop of 28 `marker info` calls.
- Workaround: that loop.

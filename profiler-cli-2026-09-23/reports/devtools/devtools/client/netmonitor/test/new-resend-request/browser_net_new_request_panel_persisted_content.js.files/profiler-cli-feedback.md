## Question: "what was the machine's CPU use at t?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --list --search "name:CPU Use"` in a zoom around the moment.
- Expected: the CPU percent of each `CPU Use` marker in the list row, as it is the only thing the marker carries.
- Got: rows with only the name and duration (`CPU Use  t=17m32s  110ms`). To get the percentages I had to use `--json` and a script over `flatMarkers[].fields`.
- What would have helped: print the `cpuPercent` field in the list's description column for `CPU Use` markers, and "Memory Used" for `Memory` markers.

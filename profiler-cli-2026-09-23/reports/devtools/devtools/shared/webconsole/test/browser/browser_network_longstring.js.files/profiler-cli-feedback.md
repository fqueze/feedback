## Instant-marker times in a long profile have no sub-second precision

Question: at what millisecond did the test's `INFO test network POST request` happen in the job's resource-usage profile, relative to the 100 ms `CPU Use` markers around it?

- Command: `profiler-cli thread markers --search "CPU Use,test network POST" --list --limit 0 --session <s>` and `profiler-cli marker info m-52 m-27 m-73 --session <s>` on a 21m52s resource-usage profile.
- Expected: start times with ms precision (e.g. `t=1272.822s` or `21m12.822s`), so a 100 ms CPU sample can be matched to a test step.
- Got: every marker shown as `t=21m13s`, and `marker info` prints `Time: 21m13s (instant)`; the CPU values are not in the list rows either.
- Workaround: `--json` and a Python script over `flatMarkers[].start` and `data.cpuPercent`.
- What the output could show: seconds with 3 decimals whatever the profile length, and the CPU percent of `CPU Use` markers in the `--list` row.

## Which marker is at index N? (checking a report's `marker=N` link) — review-browser_network_longstring.js

- Command: `profiler-cli thread markers --search ... --list --limit 0 --json`, then `profiler-cli marker info <m-…> --json` once per candidate handle.
- Expected: the `--list` rows (or their `flatMarkers[]` in JSON) to carry `markerIndex`, or a way to open a marker by index (`marker info --index 53599`).
- Got: `flatMarkers[]` has `handle`, `name`, `start`… but no `markerIndex`. Checking 15 links took about 15 separate `marker info` calls, after guessing which marker each one pointed at.
- Output that would answer it: `markerIndex` in each list row, or `marker info` accepting an index.

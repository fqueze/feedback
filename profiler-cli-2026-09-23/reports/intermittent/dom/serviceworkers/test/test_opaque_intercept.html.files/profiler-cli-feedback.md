## Question: how many ms between two log markers of a test (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search opaque_intercept --list --limit 0`
- Expected: start times precise enough to measure a ~100 ms gap between `PASS Parent exists.` and the `FAIL`.
- Got: `t=6m56s` / `t=6m57s` (second resolution) in the list output.
- Workaround: `--json` and read `flatMarkers[].start` with a Python one-liner.
- The list could print ms precision (e.g. `t=6m56.471s`) when the zoom/range is wide, or at least when markers in the list are < 1 s apart.

## Question: what was the host CPU % during a 300 ms window
- Command: `profiler-cli zoom push 415.5,417` then `thread markers --search 'name:CPU Use' --list --limit 0`
- Expected: each CPU Use marker with its cpuPercent.
- Got: only name/time/duration; the payload value needs `marker info` per marker.
- Workaround: `--json` and print `data.cpuPercent` per marker.
- The flat list could show the main payload field (cpuPercent) for CPU Use markers, as it does the label for Test markers.

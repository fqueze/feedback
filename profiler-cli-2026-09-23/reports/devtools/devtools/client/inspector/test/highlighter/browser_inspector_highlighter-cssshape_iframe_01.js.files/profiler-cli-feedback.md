## Was the machine saturated between two times? (resource-usage profile)

- Question: machine-wide CPU per second over a 17 s window of a resource-usage profile.
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 200`
- Expected: the `cpuPercent` value on each row, or a summary (min/avg/max cpuPercent) in the aggregate view.
- Got: rows with only the duration (~100 ms each) and no value; the aggregate view gives duration stats only. Had to use `--json` and jq over `.flatMarkers[].fields[] | select(.key=="cpuPercent")`, then average per second with awk.
- Could have shown: the payload's main field on each `--list` row (as Text markers do), or `counter`-style stats for CPU markers.

## CPU Use marker fields do not add up

- Command: `profiler-cli marker info m-466 --session <s> --json` (task WF8qHf6tRmmRj5o0IBroCA, resource-usage profile)
- Got: `cpuPercent 21.4%`, `User % 24.9%`, `System % 0.0%`, `Idle % 91.4%`, with rawFields `user=0.214`, `system=0.214`. User + idle > 100%, and system 0% while raw system is 0.214.
- Expected: consistent percentages. Unclear whether this is profiler-cli formatting or the harness's marker schema; I used `cpuPercent` only.

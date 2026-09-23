## Question: "what were machine CPU % and disk writes, per 100 ms interval, over this range of the resource-usage profile?"

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use,name:IO" --list --limit 0` (resource-usage profile of task GUL35uQhRfqP7yNe9iZhTw, zoomed to 71.9,72.7)
- Expected: one row per interval with its start time at ms precision and its main value (CPU Percent, Written bytes).
- Got: rows with no payload values, and every start printed as `t=1m12s`, so rows within a 1 s zoom cannot be told apart or ordered.
- Workaround: `--list --json` to collect handles, then `marker info <all handles> --json` piped through a Python script to print start + cpuPercent + write_bytes.
- What would have answered it: show the start with ms precision when the zoomed range is short (or always, e.g. `72.241s`), and print the schema's table label fields (CPU Percent, Written) in the list row, as Text markers already show their name.

## `profile info` / `counter list` on a resource-usage profile say there is nothing

- Command: `profiler-cli profile info --session <s>` and `profiler-cli counter list --session <s>` on the same resource-usage profile.
- Expected: some pointer to the machine CPU / IO tracks.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is there, as `CPU Use` / `IO` interval markers.
- Workaround: found them from `thread markers` in a zoomed range.
- Suggestion: when the profile's CPU/IO data is markers (mozharness resource-usage), say so in `profile info` instead of "No significant activity".

## (review) Question: "where does this per-test profile's t=X fall on the job's resource-usage timeline?"

- Command: `profiler-cli thread markers --session <s> --search "slotted support link" --list` on the resource-usage profile of GUL35uQhRfqP7yNe9iZhTw, then the same on the per-test profile.
- Expected: some way to map a per-test profile time (store load at 3.724–3.983 s) onto the job timeline, to read the machine's CPU/IO for exactly that span.
- Got: list rows at `t=1m13s` only; had to take both FAIL markers' `start` from `--json` and subtract by hand (offset 68.505 s).
- Also: the `CPU Use` list row shows no payload, so `iowait_pct`, the field that answered "was the disk the bottleneck", needed `--json` + a script too.
- What would have answered it: ms-precision starts in list rows, and the schema's table fields (CPU %, IO wait %, Written) printed in the row.

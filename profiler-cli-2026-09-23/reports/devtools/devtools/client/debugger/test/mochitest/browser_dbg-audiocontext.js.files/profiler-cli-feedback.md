## Question: "what was the machine's CPU use, second by second, over this range?" (resource-usage profile)

- Command: `profiler-cli zoom push 50,80 --session <s>; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- Expected: each `CPU Use` row showing its CPU Percent (or a per-bucket summary of it), since that is the only thing the marker carries.
- Got: rows with only name, time and duration (`m-185 CPU Use t=49.966s 100ms`). The value needs `marker info` per marker, or `--json` and a script to bucket `fields[cpuPercent]`.
- Workaround: `--json` piped through a Python script averaging `cpuPercent` per second.
- Also: `profile info` on the resource-usage profile says "CPU activity over time: No significant activity" and `counter list` says "No counters in this profile", although it holds 16,889 CPU Use markers. A pointer from `profile info` to those markers would have saved two commands.

## Question: "how much CPU did this thread use within the zoomed range?" (review-browser_dbg-audiocontext.js)

- Command: `profiler-cli zoom push 30.125,47.06 --session <s>; profiler-cli profile info --session <s>`
- Expected: per-thread CPU ms for the zoomed range, since the header shows the zoomed view.
- Got: the full-profile totals (Renderer 5424.029ms), unchanged by the zoom. And `thread samples` in the same range reports "1521 running samples, Graphics 100%" for a thread blocked in a futex wait that used no CPU. The non-idle split is by category, not by CPU delta.
- Workaround: `thread info`'s "CPU activity over time" (full profile only) showed that the activity ends at 30.121s.

## Same question as the entry above, again (CPU Use per second)

- `thread markers --search 'CPU Use' --list --limit 0 --json`: `flatMarkers` carry no fields either. It took feeding 281 handles to `marker info --json` and a script to get per-second `cpuPercent`.

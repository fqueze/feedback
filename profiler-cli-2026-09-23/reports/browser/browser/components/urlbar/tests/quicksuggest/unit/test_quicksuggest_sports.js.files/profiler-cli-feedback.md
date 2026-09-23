## `zoom push` silently misreads the time format profiler-cli itself prints

- Command: `profiler-cli zoom push 22m50s,24m50s --session browser-test_quicksuggest_sports.js-1` on a 53-minute resource-usage profile.
- Expected: a 2-minute zoom from 22m50s to 24m50s (the `22m50s` format is what `thread markers --list` prints), or an error.
- Got: `Zoom depth: 1`, view `ts-1→ts-2 (2s)`, so it read the values as about 22 and 24 seconds. The following `thread markers` call returned "No markers match", which looked like an empty window rather than a parse problem.
- Workaround: pass plain seconds (`zoom push 1370,1490`).

## Question: "what was the machine's CPU use over time around t=24m?" (resource-usage profile)

- Commands: `profiler-cli counter list` printed "No counters in this profile". `profile info` printed "CPU activity over time: No significant activity". `thread markers --search "name:CPU Use" --list` lists the markers but does not show their CPU %.
- What I did: `thread markers --search "name:CPU Use" --list --limit 0 --json` plus a Python script that buckets `data.cpuPercent` / `system_pct` into 20 s bins, weighted by duration.
- What the output could have shown: for a resource-usage profile, the "CPU activity over time" summary in `profile info` (or a `counter info`-style over-time table) built from the CPU Use markers, respecting the zoom. With that, "was the machine saturated when the timeout hit?" would need no script.

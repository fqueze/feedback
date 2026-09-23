## Question: was the machine busy or idle during this window of the resource-usage profile?

- Command: `profiler-cli counter list --session <s>` on a resource-usage profile (`dDDE4lzZQW-0HnM7tfIifg` profile_resource-usage.json).
- Expected: a CPU counter (or some summary) for the machine's CPU use, respecting `zoom push`.
- Got: "No counters in this profile." The machine CPU is only in per-100ms `CPU Use` markers, and `thread markers --search 'name:CPU Use'` gives count/duration stats, not the mean or max of the `cpuPercent` field.
- Workaround: `thread markers --search 'name:CPU Use' --list --limit 0 --json` over a zoom, then a Python script averaging `data.cpuPercent` per sub-window.
- What would have answered it: field statistics (mean/max) for numeric marker fields in the aggregated `thread markers` view, or exposing `CPU Use` as a counter so `counter info` works under zoom.

## Question (review): how many ms between two markers late in a long profile?

- Command: `profiler-cli thread markers --session <s> --search "browser-idle-startup-tasks-finished,name:test" --list --limit 0` on the 28-minute `dDDE4lzZQW-0HnM7tfIifg` resource-usage profile.
- Expected: start times precise enough to subtract (the gaps in question were 0.35–0.67 s).
- Got: past one minute, times print as `t=5m58s` / `t=6m1s`, rounded to the second, so sub-second gaps vanish. Adding `--json` to `--list` puts the aggregated `byType` first, and the rows are under `flatMarkers`, which is easy to miss.
- Workaround: collect the handles from the text list, then `marker info <handles> --json` and read `start`.
- What would have answered it: keep millisecond precision in `--list` for long profiles (e.g. `5m58.050s`), or add a `--relative-to <m-N>` column.

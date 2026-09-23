## `thread markers --list` rounds times to whole seconds in long profiles

- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0` on a 35-minute resource-usage profile, zoomed to 3 s.
- Expected: sub-second start times, to find the CPU Use sample covering one moment (t=293.32 s).
- Got: `t=4m53s` for ten consecutive 100 ms markers.
- Workaround: `--json` and a script reading `flatMarkers[].start` and `data.cpuPercent`.
- Question: "the machine's CPU use at t=X". The list could print ms precision when zoomed, or the CPU percent in the row.

## Marker times in `--list` are rounded to the second once a profile is over a minute long

- Command: `profiler-cli thread markers --session browser_compatibility_live-data.js-1 --category Test --search browser_compatibility_live-data.js --list --limit 0`
- Expected: millisecond start times, as for profiles under a minute (`t=12.671s`).
- Got: `t=1m41s` for both `checking for open popups` (100.799 s) and the neighbouring markers; `t=1m46s` for the failure. The 5.7 s vsync wait and the ordering within a second cannot be read from the list.
- Workaround: `marker info m-30 m-11 --json` and read `start`; for a whole list, `--list --json` and a script.

## Question: "from when does this marker stop / start happening, per window"

- Question: when did the parent's refresh drivers last really tick, and since when does innerWindowID 2 only log `RefreshDriverTick waiting for paint`?
- Command: `profiler-cli thread markers --search "name:RefreshDriverTick" --list --limit 0 --json`, then a Python script bucketing `start` per second and per `data.innerWindowID`.
- What the default output could have shown: `--group-by field:innerWindowID` gives counts per window but no first/last time. First and last start time per group (and maybe a per-second sparkline) would have answered it without a script.

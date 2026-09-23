## Question: when was each window this test created destroyed, relative to the harness's leak check (ms resolution)

- Command: `profiler-cli thread markers --session <s> --search DOMWindow,DocShell --list --limit 0` on a mochitest resource-usage profile.
- Expected: start and end times precise enough to compare with the `ShutdownLeakPathFinder | Capturing...` INFO marker, which is the whole diagnosis here (window destroyed 4-13 ms before the check).
- Got: `t=14m23s  6.601s` — start rounded to the second, no end column. Had to go through `marker info ... --json` (start/end in ms) and a Python script, and `thread markers --list --json` flatMarkers have `end: null` for interval markers, so end had to be computed from `duration`.
- What would have answered it: an `--precise`/`--ms` option or an end column in `--list`, and `end` filled in the `--json` flat list.

## Resource-usage profiles: CPU Use / Memory / IO / NetIO / Sampling Interval markers drown every unfiltered-by-name list

- Command: `profiler-cli thread markers --session <s> --list --limit 200` after `zoom push 865,885` to see what the harness logged at shutdown.
- Got: ~90% of rows are `CPU Use`, `Memory`, `IO`, `NetIO`, `Sampling Interval` (5 per ~100 ms). `--search=-name:X,-name:Y` did exclude, but needing five exclusions every time is tedious; ended up piping through `rg -v`.
- Would help: a default `--category`/type exclusion for the resource-monitor markers, or a `--category Test`-like shortcut that covers TestStatus + process output markers in resource-usage profiles.

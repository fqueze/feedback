## Marker times rounded to whole seconds past one minute, even when zoomed in
- Command: `profiler-cli zoom push 110,116 --session S; profiler-cli thread markers --session S --search <test> --list --limit 0`
- Expected: ms times inside a 6 s zoom (as under 1 min: `t=33.042s`).
- Got: `t=1m51s`, `t=1m52s`, `t=1m53s` for markers 0.6 s apart, so their order and gaps cannot be read.
- Workaround: `marker info <m> --json` one handle at a time and read `start`.

## Question: how busy was the machine in this window?
- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` on a resource-usage profile, zoomed to 6 s.
- Expected: min/mean/max of CPU Percent over the view (and `profile info` noticing the CPU data: it says "No significant activity" because the CPU is in markers, not samples).
- Got: 60 rows of durations with no CPU value in the text; had to script over `--json` `flatMarkers[].data.cpuPercent`.
- Would help: show the payload's headline field (cpuPercent) in `--list`, or aggregate numeric payload fields in the summary view.

## `marker info` with several handles: `--json` shape differs from the one-handle case
- Command: `profiler-cli marker info m-2238 m-2240 m-2302 --session S --json`
- Expected: a list of the same records the one-handle call returns (with `markerHandle`, `start`).
- Got: a different top-level shape; my `r['markerHandle']` failed with KeyError. Workaround: one call per handle.

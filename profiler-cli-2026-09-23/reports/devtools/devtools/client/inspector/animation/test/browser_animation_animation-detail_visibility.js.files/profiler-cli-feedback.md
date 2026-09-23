# profiler-cli feedback (browser_animation_animation-detail_visibility.js)

## Question: "which reflows in this range were of one document (innerWindowID 2)?"
- Command: `profiler-cli thread markers --session S --search "Reflow (sync),innerWindowID:2" --list --limit 0`
- Expected: the Reflow markers whose innerWindowID is 2 (AND).
- Got: every Reflow marker in the range (comma is OR for positive terms), with no innerWindowID column, so the list cannot answer it.
- Workaround: `--search "name:Reflow (sync)" --group-by field:innerWindowID` for counts and example handles, then `--list --json` and a script to get the times of the innerWindowID-2 ones.
- What could show it: an AND operator for positive terms (e.g. `+innerWindowID:2`), or the innerWindowID next to each row of `--list`.

## Question: "what was the machine's average and peak CPU use between t1 and t2?"
- Command: `profiler-cli thread markers --session S --search "CPU Use" --list` on the resource-usage profile (zoomed).
- Expected: a summary of the CPU Percent field over the range.
- Got: one row per 100 ms marker, no field values in the list; `--group-by` gives durations, not the field's stats.
- Workaround: `--list --limit 0 --json` and a Python script averaging `data.cpuPercent`.
- What could show it: numeric field stats (min/avg/max) in the aggregate view for markers of type CPU, or for any numeric payload field.

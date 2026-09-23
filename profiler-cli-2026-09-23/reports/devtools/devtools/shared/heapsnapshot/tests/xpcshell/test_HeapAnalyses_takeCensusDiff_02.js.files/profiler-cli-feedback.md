## Question: "how many tests of each status started before / after time t"

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python script grouping `data.status` by `start` < / >= 50.35 s.
- Default output: `--group-by field:status` gives counts over the whole view, and `zoom push` restricts to markers overlapping the range, not starting in it, so the 45 s TIMEOUT markers that start after t leak into a "before t" zoom.
- What would answer it: a start-time-only zoom/filter (e.g. `--starts-in a,b`) combined with `--group-by field:status`.

## Question: "average machine CPU over a time range" in a resource-usage profile

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --json`, then a duration-weighted average of `data.cpuPercent` (a string like `"17.9%"`) over 53.19–98.20 s.
- Default output: the list shows no CPU value per row and no aggregate.
- What would answer it: an average/min/max of `cpuPercent` for `CPU Use` markers in the current zoom, e.g. in `thread markers --search "CPU Use"` stats, or exposing it as a counter so `counter info` works.

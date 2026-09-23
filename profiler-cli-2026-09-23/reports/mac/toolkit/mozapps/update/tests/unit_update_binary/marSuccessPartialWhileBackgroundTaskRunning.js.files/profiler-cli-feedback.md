## Question: at what exact time did this marker happen, in a long profile?

- Command: `profiler-cli thread markers --session <s> --search "9962>" --list --limit 0` on a 28-minute resource-usage profile
- Expected: timestamps precise enough to line up with the per-test profile (ms resolution), e.g. `t=1322.307s`
- Got: `t=21m55s`, `t=22m2s`: whole seconds only, so two markers 7 ms apart and 900 ms apart look the same
- Workaround: `--json` and a script printing `start/1000`
- What the output could have shown: sub-second precision whenever the range is long, e.g. `t=22m2.307s`

## Question: was the machine saturated during this time window?

- Command: `profiler-cli zoom push 1298,1340` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on a resource-usage profile
- Expected: a summary of the `cpuPercent` field over the zoomed range (min/avg/max, or per-second buckets)
- Got: 351 rows with no values in the default output (the CPU percentage is only in `--json` fields)
- Workaround: `--json` and a script averaging `cpuPercent` weighted by duration, in 2-second buckets
- What the output could have shown: the field values in the list row, or an aggregate of numeric fields per marker name in the non-`--list` view

## Question: which tests were running at the same time as time T?

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0` on a resource-usage profile
- Expected: a way to filter interval markers to those overlapping the zoomed range
- Got: all ~1450 `test` markers; zooming only seems to keep markers that start inside the range, and the 170-second tests that started before it were exactly the ones I needed
- Workaround: `--json` and a script keeping `start < t1 && end > t0`
- What the output could have shown: an `--overlapping` option, or overlapping interval markers kept when zoomed

## Question (review): which marker does a link's `marker=N` point at?

- Command: `profiler-cli thread markers --search 'testing-common is not substituted' --list --limit 0`, then `profiler-cli marker info m-3250`
- Expected: the marker's index, next to its handle, so it can be compared with the link's `marker=N`
- Got: neither the list row nor `marker info` shows the index; it is only `markerIndex` in `marker info --json`. Checking 7 links meant a shell loop piping each `--json` into python
- Workaround: `for m in ...; do profiler-cli marker info $m --json | python3 -c '...markerIndex...'; done`
- What the output could have shown: `index N` in the `marker info` header, or a `thread markers --index N` lookup that goes straight from the link to the marker

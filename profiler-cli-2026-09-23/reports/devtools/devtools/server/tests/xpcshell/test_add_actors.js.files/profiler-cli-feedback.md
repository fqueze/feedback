## `zoom push` silently misparses minute-form times

- Command: `profiler-cli zoom push 4m28s,5m0s --session test_add_actors.js-1`
- Expected: a zoom from 268 s to 300 s (the marker list prints times as `4m28s`, so it is natural to paste them back), or an error.
- Got: `Pushed view range: ts-1 (4s) to ts-2 (5s)`. The zoom silently went to 4 s–5 s, and the following marker query returned the wrong window without any warning.
- Workaround: pass plain seconds (`zoom push 268,300`).

## Question: "was the machine saturated during this window?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `data.cpuPercent` per 5 s / 15 s bucket.
- The resource-usage profile has no counters (`counter list`: none), so the whole-machine CPU exists only as `CPU Use` interval markers. Their value is shown only by `marker info`, one marker at a time. The aggregated `thread markers` view gives durations, not the `CPU Percent` field.
- What would have answered it: a mean/min of the `cpuPercent` field in the aggregate for a zoom range (e.g. `thread markers --search "CPU Use" --stats-field cpuPercent`), or exposing these markers as a counter so that `counter info` over a zoom works.

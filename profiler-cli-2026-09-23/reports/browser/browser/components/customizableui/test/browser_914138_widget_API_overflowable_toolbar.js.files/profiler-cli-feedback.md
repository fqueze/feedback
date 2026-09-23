## Question: how busy was the machine while one test ran (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>` (zoomed to a test's `test` marker range).
- Expected: the CPU percent of each 100 ms sample in the row, or a summary (min/avg/max) of `cpuPercent` over the zoom range.
- Got: rows with only name, time and duration (`CPU Use  t=15m1s  102ms`); the percentage needs `marker info` per marker or `--json` and a script.
- Workaround: `--json` piped to python, reading `flatMarkers[].fields[key=cpuPercent]`.

## Question (review): which handle has markerIndex N, to check a link's `marker=N`

- Command: none exists; had to `zoom push` around the quoted time, `thread markers --list`, then `marker info m-a..m-b --json` and match `markerIndex` in a script.
- Expected: `marker info --index 43347` or `thread markers --search markerIndex:43347`.
- Workaround: the zoom/list/json loop above, per link.

## `thread markers --search "resize - window"` matches nothing

- Expected: the DOMEvent rows the list prints as `resize - window`.
- Got: 0 markers; the label is composed from payload fields, not a single value. `--search eventType:resize` works.

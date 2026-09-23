## Machine CPU over a window of a resource-usage profile needs a script

- Question: "was the machine busy while this test's 45 s ran (t=53.2 s to 98.3 s)?"
- Command: `zoom push 53.2,98.3`, then `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script.
- Expected: the aggregated `thread markers --search "name:CPU Use"` view to give min/mean/max of `cpuPercent` over the view.
- Got: durations only. In the JSON, `cpuPercent` is a string (`"58.9%"`), so even the script had to strip `%` before averaging.

## Where a burst of `test` markers of one status begins needs a script

- Question: "when did the harness start failing to launch tests, and how many 45 s TIMEOUTs were there?"
- Command: `thread markers --search name:test --list --limit 0 --json`, then Python grouping `data.status` and duration.
- What would have answered it: the aggregated `name:test` view broken down by `status`, with count, first/last start and min/max duration per status (or `--group-by field:status`, if it gave start ranges).

## (review) Checking a link's `marker=N` among many identical markers costs one call per candidate

- Question: "which of the 1,149 identical `not killing -- proc or pid unknown` markers is index 11447, the one this report links?"
- Command: `thread markers --search "not killing" --list --limit 0 --json` (after `zoom push 98.0,98.4`), then `marker info <m-…> --json` for each of about 80 candidates in 98.2 to 98.3 s.
- Expected: `markerIndex` in each `flatMarkers` entry, or a way to look a marker up by index (`marker info --index 11447`).
- Got: handles only, so I had to call `marker info` once per candidate.

## (review) A bare `--search` term misses a value the list itself prints

- Command: `thread markers --search "run-tests" --list --limit 0`
- Expected: the `Phase` marker the list prints as `run-tests — CPU time: 16m46s (11.4%)`.
- Got: `No markers match`. `run-tests` is only in `rawFields` (`phase`), which the bare term does not search. `--search Phase` found it.

## Question: "exactly when did this marker start" (sub-second times past the first minute)

- Command: `profiler-cli thread markers --session <s> --search test_stepping-10 --list --limit 0`
- Expected: start times precise enough to compare markers tens of ms apart (a test start and its "will retry" 39 ms later).
- Got: `t=2m14s` for both, rounded to the second once past 60 s; `marker info` prints the same `Time: 2m14s`.
- Workaround: `--json` and read `flatMarkers[].start` (133507.908 vs 133546.908).
- Output could show: milliseconds (`t=2m13.508s`) in list rows and in `marker info`.

## Question: "how many tests started after time T, by status"

- Command: `profiler-cli thread markers --search name:test --list --limit 0` (621 `test` markers in a resource-usage profile).
- Expected: a way to group markers by a payload field within a zoom range, e.g. `zoom push 131.557,end` then `--group-by field:status`.
- Got: needed `--json` and a script to split the Test markers by start time and count `data.status`.
- Workaround: python over `flatMarkers`. (If `--group-by field:status` already works inside a zoom, the guide does not say that the zoom filters on marker start rather than overlap, which is what this question needs.)

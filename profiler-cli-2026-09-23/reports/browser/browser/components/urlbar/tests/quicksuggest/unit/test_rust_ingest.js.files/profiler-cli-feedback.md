# profiler-cli feedback (browser-test_rust_ingest.js)

## Question: "was the machine saturated during this window?" (resource-usage profile)
- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` after `zoom push 1335,1360`
- Expected: each CPU Use marker's `CPU Percent`, or a summary (min/avg/max CPU %) for the zoomed range.
- Got: 202 rows with only name, time and duration; the percentages are in the payload only.
- Workaround: `--json` and a Python script averaging `cpuPercent` per second. A per-range CPU summary for resource-usage profiles would answer this directly.

## Stacks on "C++ warning" markers of the resource-usage profile are `unknown!null`
- Command: `profiler-cli marker stack m-7 --session S` (marker list flags it with a stack)
- Expected: no stack indicator, since there is nothing to show.
- Got: `[1] unknown!null`. The marked-as-having-a-stack flag sent me looking for nothing.

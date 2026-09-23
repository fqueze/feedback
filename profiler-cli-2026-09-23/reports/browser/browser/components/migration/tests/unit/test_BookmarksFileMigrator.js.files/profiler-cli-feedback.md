## Question: "what was the machine's CPU use over this time window?" (resource-usage profile)
- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0 --json`, then a python script averaging `cpuPercent` weighted by duration into buckets.
- The resource-usage profile has no counters (`counter list` is empty), so CPU is only in ~20,000 `CPU Use` interval markers, and the default text output does not aggregate their payload.
- What the output could show: a duration-weighted mean/min/max of a numeric marker field over the zoomed range (e.g. `thread markers --search "CPU Use" --stats cpuPercent`), or expose CPU Use as a counter so `counter info` with zoom answers it.

## `marker info ... --json` records have no `handle`
- Command: `profiler-cli marker info m-113 m-121 --session S --json`
- Expected: each record to carry its handle, since several were requested.
- Got: `handle` missing (None), so matching records back to the handles means relying on order.

Correction to the entry above: `marker info --json` does carry the handle, as `markerHandle`. The actual problem is smaller: `thread markers --json` names it `handle` and `marker info --json` names it `markerHandle`, so a script written for one reads `None` from the other.

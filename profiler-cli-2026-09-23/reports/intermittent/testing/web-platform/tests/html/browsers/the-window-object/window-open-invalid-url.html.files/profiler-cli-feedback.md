## Machine CPU over a time range in a resource-usage profile needs a script
- Question: "was the machine busy, and was one core pegged, during this 45 s test / during each stall?"
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` (with `zoom push m-1`), then a Python script to bucket `cpuPercent` per second and take min/mean.
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (min / median / mean, maybe per-second sparkline), the way `counter info` does for counters.
- Got: 450 individual 100 ms markers per 45 s; `counter list` says "No counters in this profile", since the resource profile stores CPU as markers.
- Workaround: the JSON dump plus a script.
## C++ warning markers in resource-usage profiles have a useless stack
- `profiler-cli marker stack m-125` on a `C++ warning` marker flagged ✓ (has stack) prints only `[1] unknown!null`. The ✓ suggests a stack worth opening; it is not.

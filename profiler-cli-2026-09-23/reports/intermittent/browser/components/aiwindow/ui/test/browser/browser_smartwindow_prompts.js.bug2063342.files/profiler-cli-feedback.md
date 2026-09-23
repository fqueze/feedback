## Average machine CPU over a test's run needs a script over `--json`

- Question: was the machine saturated while this test ran? (resource-usage profile, `CPU Use` markers)
- Command: `profiler-cli zoom push 2436.6,2529.9 ...; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`
- Expected: `counter list` / `counter info` or `thread info` to show machine CPU for the zoomed range, as they do for process CPU in per-test profiles.
- Got: `counter list`: "No counters in this profile". `profile info` / `thread info`: "No significant activity". The CPU data exists only as 41,246 `CPU Use` markers, whose JSON fields are strings with a percent sign (`cpuPercent: '100.0%'`), so a first attempt at summing them failed with a TypeError.
- Workaround: a python one-liner computing the duration-weighted mean of `cpuPercent` and `idle_pct`. Could show: an aggregate (mean, time at >= 90%) for CPU Use markers in the current zoom, e.g. in `thread markers` stats or as a synthesized counter.

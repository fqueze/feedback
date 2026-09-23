## Machine CPU over one test marker in a resource-usage profile (browser.storage.extension.js, 2026-09-22)

- Question: "was the machine busy or idle during this 10 s timeout?"
- Command: `profiler-cli zoom push m-1 --session <s>` then `profiler-cli counter list` (says "No counters in this profile") and `profiler-cli thread markers` (shows 104 `CPU Use` markers, but only counts and durations).
- Expected: an aggregate of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean/max), as `counter info` gives for real counters; `profile info` also says "No significant activity" for the whole job, which reads as if no CPU data existed.
- Got: had to `thread markers --search "name:CPU Use" --list --limit 0 --json` and average `data.cpuPercent` in a Python script.
- Suggestion: treat the resource-usage profile's `CPU Use` markers as a counter (or add a field-aggregate option to `thread markers`, e.g. `--stats cpuPercent`).

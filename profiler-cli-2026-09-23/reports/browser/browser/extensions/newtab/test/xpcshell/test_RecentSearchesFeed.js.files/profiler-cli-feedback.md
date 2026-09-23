## Question: what was the machine's CPU use over this time range? (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>` on a job's `profile_resource-usage.json`, zoomed to the failing task.
- Expected: one row per sample with its CPU % (and idle %), or a counter-style summary over the zoom.
- Got: rows with only name, start and duration (`CPU Use  t=3m25s  187ms`); the percentages need `marker info` per handle or `--json` plus a script. `profile info` on the same profile says "CPU activity over time: No significant activity" and `counter list` says "No counters", which reads as "no CPU data" although 4,641 `CPU Use` markers are there.
- Workaround: `--json` and a python one-liner printing `data.cpuPercent` / `data.idle_pct`.
- Could have shown: the payload's main field in the list row, or `profile info` summarising `CPU Use` markers like a counter.

## Question: at what exact time did these markers start? (ordering events inside a 1-2 s task)
- Command: `profiler-cli thread markers --search ... --list` inside a zoom on a 1.9 s task.
- Expected: millisecond start times.
- Got: `t=1m9s` / `t=1m11s` for every row once the profile passes one minute, so rows 50 ms apart are indistinguishable.
- Workaround: `--json` and a script printing `start`.
- Could have shown: `t=69.236s` (or ms precision whenever the view is short).

## Question: how much CPU did this thread (or process) get over this range, as a share of one core?
- Command: `profiler-cli counter info c-0 --session <s>` (Process CPU), zoomed to a 1.6 s CC slice; and `thread samples --include-idle`.
- Expected: absolute CPU time for the range (e.g. "131 ms of CPU in 1.591 s, 8% of one core"), for the counter and for the thread's `threadCPUDelta`.
- Got: `Unit: percent` with bucket values such as "5.9%", which are relative to the counter's own peak over the whole profile (about 1.6 cores here, via `computeMaxCounterSampleCountPerMs`). Nothing says so, and they read like a share of one core or of the machine. `thread samples` gives only sample counts, no CPU time.
- Workaround: downloaded the profile and summed `samples.threadCPUDelta` in python. Its unit is µs on macOS and ns on Linux (`meta.sampleUnits`).
- Could have shown: the counter's absolute CPU time for the range and what 100% means, plus a thread CPU-time total in `thread info` or `thread samples` for the zoom.

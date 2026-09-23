## Question: "how far apart were this thread's samples, and how much CPU did it use in each?"

- Context: a macOS xpcshell profile at a 10 ms interval held 66 main-thread samples over 6.5 s, and a 262 ms LongTask had 8 samples all showing the same stack in `mach_absolute_time`. Whether the thread was running (busy), blocked, or descheduled decided the diagnosis.
- Commands tried: `thread info`, `thread samples --include-idle`, `profile info` (zoomed). They give sample counts and a coarse "CPU activity over time" summary, but not per-sample timestamps, gaps, or `threadCPUDelta`.
- Workaround: downloaded the raw profile (200 MB) and summed `samples.timeDeltas` / read `samples.threadCPUDelta` in Python. That showed median sample gaps of 38 ms (p90 84 ms) and 7 consecutive samples with 0 us CPU during the LongTask.
- What would have answered it: a `thread samples --list` (or `thread sample-times`) giving each sample's time, gap from the previous one, CPU delta and leaf frame, honoring zoom.

## Question: "what was the machine's CPU use while this test ran?"

- Context: resource-usage profile, `CPU Use` markers, zoomed on the test's `test` marker (376 markers in 37 s).
- Command: `thread markers --search "name:CPU Use" --list --limit 0` lists the markers but not their `CPU Percent` field, so the answer needs `marker info` on each one.
- Workaround: `--json` plus a Python script to get min/median/mean. First run: 100% on all 376 samples. Retry: median 27.8%, max 54%.
- What would have answered it: a numeric-field summary (min/median/max) for a marker field over the current zoom, e.g. `thread markers --search "name:CPU Use" --stats cpuPercent`, or showing the payload's main field in the `--list` rows.

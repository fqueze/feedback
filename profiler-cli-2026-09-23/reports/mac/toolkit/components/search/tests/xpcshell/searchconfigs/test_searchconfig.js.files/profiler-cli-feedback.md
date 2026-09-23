## Question: was the machine CPU-saturated during one test's run? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` on a `profile_resource-usage.json`
- Expected: an aggregate of the `CPU Use` markers' `cpuPercent`/`idle_pct` over the current zoom (or per time bucket), like `counter info` prints for counters ("over time" buckets). `profiler-cli counter list` says "No counters in this profile", so the machine CPU track is only reachable as 11,000 interval markers.
- Got: one row per 100 ms marker with no values shown; the percentages are only in `--json` `.flatMarkers[].data`.
- Workaround: `--json` piped to jq + awk to average `cpuPercent` per 15 s bucket.
- What would have answered it: `thread markers --search "name:CPU Use"` summarising the numeric fields (min/avg/max, or an over-time sparkline like `counter info`) for the zoomed range.

## Question: how much CPU did this thread get? (Windows per-test profile, from review-test_searchconfig.js)

- Command: `profiler-cli profile info --session <s>` on `VDa6cQYDQ92mFWhLGQclUw/.../profile_test_searchconfig.js.json` (`meta.sampleUnits.threadCPUDelta` = `ns`, Windows cycle counts converted by Gecko)
- Expected: one CPU figure for GeckoMain, or a warning that the CPU deltas are inconsistent.
- Got: two contradictory figures with no warning: `t-0: GeckoMain - 154450.061ms` for a 2m31s profile (impossible for one thread), and `50% for 75817.4ms` under "CPU activity" (per-sample ratios clamped to 100%). In the raw samples, 45% of deltas exceed their sample's elapsed time, up to 2.77x; 40% are 0. Neither printed figure is a CPU time. The first review of this report took the 75.8 s at face value.
- Workaround: downloaded the profile JSON and computed `threadCPUDelta / (timeDeltas * 1e6)` per sample.
- What would have answered it: when the CPU deltas exceed elapsed time in a notable share of samples, say so next to both figures (and the max ratio seen), rather than silently clamping one and summing the other.

## Question: when the thread got no CPU, was it preempted or blocked?

- Command: none available; `thread samples` has no filter on per-sample CPU usage.
- Expected: something like `thread samples --cpu-below 1%` (and `--cpu-above`), to compare the leaf frames of samples that got no CPU with those that did: same compute frames means preempted, a wait or syscall frame means blocked.
- Got: nothing to filter on.
- Workaround: downloaded the profile JSON and scripted the leaf-frame counts for zero-CPU vs non-zero samples.
- Also, same shape as the entry above: the peak `Memory` marker value (`used`) over a zoomed range needed `--json` and a script; `--group-by field:used` groups by exact byte value.

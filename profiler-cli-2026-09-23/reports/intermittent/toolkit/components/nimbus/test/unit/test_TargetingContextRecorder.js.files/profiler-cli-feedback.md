
## Question: the whole machine's CPU use during one test's run (resource-usage profile)
Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>`
Expected: a summary (mean / fraction saturated) of the CPU Use markers over the zoomed range, like `counter info` gives for counters.
Got: 609 individual 100ms markers, each needing `marker info` to see its percentage; `counter list` says no counters in this profile.
Workaround: `--json` and a python script averaging data.cpuPercent. The output could have shown 'CPU Use: mean 100%, 100% of samples >= 95%' for the view.


## Question (review): was the sampler starved, and when? (samples per second over time)
Command: `profiler-cli zoom push <a>,<b>` then `thread samples --include-idle --limit 1`, grepping `Categories (N running samples)`, repeated for 15 ranges.
Expected: `thread info` / `profile info` to show the sample count per time bucket, or the achieved rate against the configured interval ("918 samples, 15/s against 100/s configured").
Got: only the total sample count, and "CPU activity over time", which counts CPU and not samples.
Workaround: the zoom loop above. The output could have shown the sample rate per bucket next to the CPU activity.

## Question (review): how far apart are consecutive markers of one kind (TEST-PASS cadence)?
Command: `profiler-cli thread markers --search name:TEST-PASS --list --limit 0 --json` + a python script computing gaps.
Expected: a summary of the spacing between the matching markers (median/min/max gap, longest gaps with their times), e.g. with `--gaps`.
Got: 962 rows with absolute times only.
Workaround: the script. The output could have shown "gaps: median 8 ms; longest 7.6 s at t=34.149 s, 7.3 s at t=41.747 s".

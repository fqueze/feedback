## `thread markers --list` times are rounded to whole seconds in long profiles

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_manage_tabs_tool --list --limit 0 --session <s>` on a 41-minute profile.
- Question: "how long did each add_task take?" (`Entering test X` to `Leaving test X`), or "how long did each `Opening new AI Window` → `Waiting for AI window attr` take?"
- Got: `t=39m49s`, `t=40m25s`, ... With subtests 3 to 9 s long, 1 s resolution is not enough, so I used `--json` and a script to pair Entering/Leaving markers.
- Suggestion: print milliseconds (`t=2389.399s`) in list mode, or add a way to pair Entering/Leaving test markers into per-subtest durations.

## Resource-usage profile: `profile info` says "No significant activity"; `counter list` says "No counters"

- Command: `profiler-cli profile info` / `profiler-cli counter list` on `profile_resource-usage.json` (Windows and Linux jobs).
- Question: "was the machine saturated while this test ran?"
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The whole-machine CPU is there as `CPU Use` markers (`cpuPercent`, `idle_pct`), one per ~100 ms, and I had to zoom and script over `--json` to average them over the test's range (33 % on Windows, 53 % on Linux).
- Suggestion: have `profile info` (or `thread markers` on `CPU Use`) summarize the `cpuPercent` over the current zoom: mean, median, and time spent above 90 %.

## `thread samples --include-idle` calls idle samples "running samples"

- Command: `profiler-cli thread samples --include-idle --limit 1 --session <s>`, zoomed to one test's range.
- Question: "what fraction of this range was the main thread idle?"
- Got: `──── Categories (6567 running samples) ────`, and further down `Idle 90 1.4%`. The 6567 includes the 90 idle samples. The report under review read it as "6567 running samples against 90 idle", in two observations.
- Suggestion: with `--include-idle`, label the header `6567 samples (6477 running, 90 idle)`.

## Which call path each of many identical markers came from

- Question: 17 `console.error` markers with the same message in one resource-usage profile; which were logged from `RemoteSettingsExperimentLoader.enable` (startup) and which from the `UpdateTimerManager` timer?
- Command: `profiler-cli thread markers --session <s> --search "nimbus-desktop-experiments" --list --limit 0`
- Expected: some way to tell them apart in the list, e.g. `--group-by stack` / a column with the first frame where their stacks differ, or `--search` matching stack frames.
- Got: identical rows; the stack is only visible per marker.
- Workaround: a shell loop over `profiler-cli marker stack m-N` for each handle, grepping the frame. 17 invocations.

## How busy the machine was over a time range (review-test_console_assert.html)

- Question: average machine CPU and iowait between two times (the first browser's startup vs a later one's), in a resource-usage profile.
- Command: `profiler-cli counter list --session <s>`
- Expected: a CPU track, or a way to summarize the per-100 ms `CPU Use` markers over the current zoom (avg/max of `cpuPercent`, `iowait_pct`).
- Got: "No counters in this profile." The CPU data exists only as `CPU Use` markers, and `thread markers --search "CPU Use"` lists them one row per 100 ms with no aggregate of their fields. The original report concluded from this that the profile had no CPU data.
- Workaround: `thread markers --search "CPU Use" --list --limit 0 --json`, then a Python script over `flatMarkers[].data`.

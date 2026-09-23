## Question: "which tests were running at time T?" (intervals overlapping an instant)

- Command: `profiler-cli thread markers --session <s> --category Tasks --list --limit 0 --json` piped to a python script keeping markers with start < T < end.
- Expected: a way to filter interval markers by overlap with a time or range, e.g. `--overlapping 44.918` or `zoom push` including intervals that started before the zoom.
- Got: `zoom push a,b` + `--list` shows markers *starting* in the range; the long-running tests that started earlier and were still running at T (the leaker suspects) are only found by scripting over `--json`.

## Question: "what was the machine's CPU use over time?" on a resource-usage profile

- Command: `profiler-cli profile info` said "CPU activity over time: No significant activity."; `profiler-cli counter list` said "No counters in this profile."
- Expected: the CPU use the profile does hold (the `CPU Use` interval markers, with `cpuPercent`) summarised over time, like the "CPU activity over time" section does for samples.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a python script bucketing `data.cpuPercent` by 5 s.

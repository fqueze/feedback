## Question: in which order did harness events happen within the same second?

- Command: `profiler-cli thread markers --session <s> --search "Launched Test App,launch_application,get_process_list,Failed to start" --list --limit 0`
- Expected: timestamps precise enough to order events that are tens of ms apart (the xpcshell harness logs 10 parallel slots' launches, `ps` listings and failures within the same second).
- Got: `t=3m14s` for a dozen markers spread over 3m13.4s–3m14.9s, so their order could not be read; the list is in time order, but the gaps (which decided the diagnosis: a pid named `:xpcshell5` at 187.664 and `:xpcshell9` at 187.778) are invisible.
- Workaround: `--json` and a script over `flatMarkers[].start`.
- What the output could show: millisecond timestamps in `--list` when the view is zoomed under a minute, or a `--precise-times` flag.

## Question: how busy was the machine over time, in a resource-usage profile? (review)

- Command: `profiler-cli profile info --session review-test_deduplicatePaths_01.js-3` on the Windows resource-usage profile of task PyUxuOpdQj2b3T3XyQOKpg.
- Expected: the machine's CPU use over time, which this profile records as `CPU Use` interval markers (`cpuPercent`), 100% over 40-50 s and about 2% over 60-130 s.
- Got: `CPU activity over time: No significant activity.`, and `thread samples` reports no samples: the only CPU data is in markers, which neither summarizes.
- Workaround: `thread markers --search "name:CPU Use" --json --list --limit 0` and a duration-weighted average of `cpuPercent` per window in a script.
- What the output could show: for a profile with `CPU Use` markers, `profile info`'s CPU activity section built from them.

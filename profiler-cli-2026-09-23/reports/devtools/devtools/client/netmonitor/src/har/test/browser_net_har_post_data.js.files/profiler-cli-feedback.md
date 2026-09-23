## Question: "what was the machine's CPU use during this interval?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0` after `zoom push m-<test marker>`
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean, max, or per-bucket), since that is the question a timeout raises.
- Got: 470 individual markers, each needing `marker info` to see its percentage; `counter list` says "No counters in this profile" and `profile info` says "No significant activity", which reads as "no CPU data" although the markers carry it.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per 5 s bucket.

## Default thread after load differs between profiles

- Command: `profiler-cli load <per-test profile URL> --session X` then `thread markers --category Test ...`
- Expected: the parent process GeckoMain selected by default, as it was for another per-test profile of the same test.
- Got: for task LKgkpxhFSye4qHcIpie0UQ (macOS) the default was t-40 (Privileged Content), and the Test-marker query silently returned 0 markers.
- Workaround: `profile info --search "Parent Process"` then `thread select t-0`.

## Question: "which activity subtypes did the socket thread report for this channel?"

- Command: `thread markers --search ObserveActivityWithArgs --list` in a zoom
- Expected: nothing more is possible from the marker itself (the runnable name has no subtype), noted only because counting and timing these runnables was the only way to compare a hung POST with a successful one.
- Got: the runnable names, durations, and task ids; the `$NF` of the list line is the task id, not the name, so `awk` on the default output mangled the name column.
- Workaround: `sed 's/ - priority.*//'` on the list output.

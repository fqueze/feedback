## Question: how busy was the machine when the click happened (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list` on a job's `profile_resource-usage.json`, zoomed on 1154.5,1155.3.
- Expected: each `CPU Use` row to show its `cpuPercent` / `idle_pct`, the only thing that marker carries.
- Got: `m-865  CPU Use  t=19m14s  100ms  ✗` with no value; `profile info` says "No counters in this profile" and "CPU activity over time: No significant activity", so nothing points at the CPU data either.
- Workaround: `--list --json` piped through python to print `fields[].cpuPercent`. The default list could show the marker's description fields (as it does for Text markers), and `profile info` could mention the CPU Use markers when there is no CPU counter.

## Question: what was the main thread running in a 70 ms window

- Command: `profiler-cli thread samples-top-down --session <s>` zoomed on 49.190,49.26 (parent GeckoMain of a CI per-test profile).
- Expected: a tree, or a sentence saying the range has no non-idle samples.
- Got: the header `Top-Down Call Tree:` followed by nothing.
- Workaround: `--include-idle` showed it was all idle; `thread info` showed the thread has only 1820 samples over 94 s (about 50 ms apart). Saying "0 non-idle samples in this range (N idle); sampling interval here is ~X ms" would have saved two calls.

## Question: in what order did these runnables run within one millisecond (review)

- Command: `profiler-cli thread markers --session <s> --list --limit 0`, zoomed on 49.190,49.215 (parent GeckoMain).
- Expected: enough time precision to tell whether a `waitForCondition` interval marker ended with one `tryOnce` runnable or with the next, and whether the flush ran before a given runnable.
- Got: every row printed `t=49.199s`; start and end in µs were only in `marker info --json`.
- Workaround: loop over `marker info m-N --json` with python to print `start`/`end`. Printing sub-ms start times once a zoom is under ~50 ms, or an end column for interval markers, would answer it directly.

## Question: "how busy was the machine at t" in a resource-usage profile

- Commands: `profiler-cli profile info --session ContentCrashTest.kt-1` then `profiler-cli thread markers --session ContentCrashTest.kt-1 --search "name:CPU Use" --list --limit 0` (after `zoom push 198,201.5`)
- Expected: the CPU percent over the window of the failure.
- Got: `profile info` says "Platform: Unknown", "0.000ms" CPU and "CPU activity over time: No significant activity" for a profile whose CPU Use markers show 65-80% host CPU; the marker list shows 35 `CPU Use` rows with only their durations, no CPU Percent.
- Workaround: `profiler-cli marker info m-72 m-97 m-98` one by one to read "CPU Percent".
- Suggestion: for a mozharness resource-usage profile, summarize the `CPU Use` markers' CPU Percent in `profile info` (and in the zoomed range), or show the main payload field in `--list` rows.

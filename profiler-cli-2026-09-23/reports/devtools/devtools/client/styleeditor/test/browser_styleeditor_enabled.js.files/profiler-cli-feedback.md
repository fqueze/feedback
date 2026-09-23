## Question: "how busy was the machine between t1 and t2?" (resource-usage profile)

- Command: `profiler-cli zoom push 664.5,667.2 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percentage of each `CPU Use` marker in the list, or a min/avg/max summary for the zoomed range.
- Got: one row per marker with only its name, time and duration; no CPU value. `counter list` says "No counters in this profile", and `profile info` says "No significant activity" for the whole job, which reads as "machine idle" but only means the one pseudo-thread has no samples.
- Workaround: `--list --json` and a script averaging `flatMarkers[].data.cpuPercent`. Showing the CPU % in the list rows (or a summary line for `CPU Use` markers in the view) would have answered it directly.

## Question: "which marker is the failure whose stack says `not found in CC graph at 0x…`?" (review)

- Command: `profiler-cli thread markers --session <s> --search "not found in CC graph" --list --limit 0`
- Expected: the `TestStatus` FAIL marker, whose stack is `serial=30` / `not found in CC graph at 0x29aa544c800`.
- Got: `0 markers`. `--search` does not look at a TestStatus marker's stack, which is where the harness puts the failure detail.
- Workaround: search the message (`leaked window until shutdown`) instead, then `marker info` to read the stack. Matching stack text for Test markers, or saying that the stack is not searched, would have avoided the empty result.

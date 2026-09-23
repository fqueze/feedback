## Question: "what was the machine's average CPU use while this test ran?"

- Command: `profiler-cli zoom push 280,344 --session <s>` then `profiler-cli thread markers --search 'CPU Use' --list --limit 0 --json --session <s>` on a resource-usage profile, then a Python script averaging `data.cpuPercent`.
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean / median / max, or a sparkline), e.g. in the `thread markers` aggregate view for a marker type with a numeric field.
- Got: only per-marker values (643 of them); `counter list` says "No counters in this profile", so the CPU track is only available as markers.
- Workaround: `--json` and a script.

## (review) `thread samples` keeps the popped zoom range

- Command: `profiler-cli zoom push 10,12 --session S`, `thread samples --include-idle --session S` (59 samples), `zoom pop --session S`, then `thread samples --include-idle --session S` again, on the macOS per-test profile of JJrlzAsKThaZMpcdRC1fLA.
- Expected: the full profile's 926 samples, as the header `View: Full profile` says.
- Got: the header says `View: Full profile`, but the categories still count the 59 samples of the popped range, with and without `--include-idle`. `zoom clear` answers "Already at full profile view" and does not fix it.
- Workaround: `zoom push` over the whole profile, then `zoom pop`: the next `thread samples` counts all samples again.

## (review) Question: "how much CPU did the main thread use during this task?"

- Command: `profiler-cli zoom push m-6 --session S` (a `task` marker), then `profiler-cli profile info --session S`.
- Expected: each thread's CPU time within the zoomed range, to compare the CPU share of one task with another.
- Got: the whole profile's CPU time (`t-0: GeckoMain - 15563.989ms`) for every zoom; `counter info` of the process CPU counter follows the zoom, but only as a percentage per bucket.
- Workaround: none; used the per-bucket process CPU percentages over the whole profile.

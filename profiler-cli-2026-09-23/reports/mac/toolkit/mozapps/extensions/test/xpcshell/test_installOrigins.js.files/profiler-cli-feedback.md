# profiler-cli feedback (test_installOrigins.js)

## `thread markers --search` comma is OR, so a name filter plus a term widens instead of narrowing

- Command: `profiler-cli thread markers --session <s> --search "name:Runnable,SetProcessSandbox" --list --limit 0` on a parent main thread with 398,729 `Runnable` markers.
- Expected: the `Runnable` markers whose text contains `SetProcessSandbox` (AND), a handful of rows.
- Got: every `Runnable` marker (the positives are OR'd, as `thread markers --help` says). On the parent main thread that is ~400,000 rows; the command ran past 120 s and I had to kill it, while the same command on content threads silently returned thousands of rows I then grepped.
- Workaround: `--search SetProcessSandbox` alone, then grep for `Runnable`.
- What would help: an AND form for `thread markers --search` (samples commands already use comma as AND, so the two meanings of comma are easy to mix up), or a warning when a `field:value` term is OR'd with a bare term.

## No way to get the sampling gaps of a thread

- Question: "is the sampler starved?" The parent main thread had 489 samples in 30.8 s at a 10 ms interval.
- Command: `profiler-cli thread info` gives the sample count; nothing gives the distribution of time between samples (median/max gap) for the view, which is what says whether samples are missing evenly (starvation) or in one hole.

## (review) How much of a thread's awake time was it actually on CPU?

- Question: was the test's main thread starved of CPU or waiting? Answered by comparing `Awake` durations with their `CPU Time` field.
- Command: `profiler-cli thread markers --session <s> --search "name:Awake" --list --limit 0 --json`, then a script to sum `duration` and `data["CPU Time"]` (26.7 s awake vs 6.6 s CPU on FeE6's parent main thread), and to sort the stretches by duration.
- What would have answered it: `thread info` printing awake time next to CPU time for the view, and the longest `Awake` stretches with their CPU share. Its "CPU activity over time" gives the CPU but not the awake time it should be compared against.

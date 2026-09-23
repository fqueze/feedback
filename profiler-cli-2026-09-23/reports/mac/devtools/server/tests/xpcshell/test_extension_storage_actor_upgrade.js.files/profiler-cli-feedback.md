## Question: was the machine saturated while this test ran? (resource-usage profile)

- Command: `profiler-cli zoom push m-2 --session tesau-2` (the test's `test` marker), then `profiler-cli thread markers --session tesau-2 --search "name:CPU Use" --list --limit 0`
- Expected: each `CPU Use` row showing its CPU percent (or `profile info` / `counter list` summarising machine CPU for the zoomed range).
- Got: rows with handle, time and duration only; the payload (`cpuPercent`, `idle_pct`) is not printed. `profile info` says "No significant activity" and `counter list` says "No counters in this profile", so nothing in the default output answers the question.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].data.cpuPercent`.
- What the output could have shown: the `cpuPercent` / `idle_pct` fields inline in the list, or a CPU-over-time sparkline for resource-usage profiles in `profile info`.

## Question: at what times were the main thread's few samples in a 20-75 ms window, and did the thread use CPU in each?

- Command: `profiler-cli zoom push 2.3243,2.3995 --session tesau-5` then `profiler-cli thread samples-top-down --include-idle`
- Expected: a way to list the samples in the range with their timestamps, stacks and CPU delta (to tell a preempted thread, repeated identical stacks at 0 CPU, from a running one).
- Got: an aggregated tree only; percentages hide that there are 1-4 samples. To place them I bisected with repeated `zoom clear` / `zoom push <sub-range>` and `--json` `regularCallTree.totalSamples`; CPU per sub-range only came indirectly from `thread info`'s "CPU activity over time".
- What the output could have shown: sample count in the tree header (it is in the JSON), and a `thread samples --list` mode with time, CPU delta and leaf frame per sample.

## Question: the machine's CPU use around a given time, in a resource-usage profile

- Commands: `profiler-cli profile info`, `profiler-cli counter list`, `profiler-cli thread samples --include-idle` (after `zoom push 393.5,395.5`), on `profile_resource-usage.json` of task SavkcSEVTlmGVk-z--wzmA.
- Expected: a CPU-over-time readout for the range, such as a sparkline or per-bucket percentages.
- Got: "CPU activity over time: No significant activity.", "No counters in this profile.", and "No samples in the current view". The data exists only as one `CPU Use` marker per 100 ms, and `thread markers --search 'name:CPU Use' --list` prints no percentages. To see them I had to collect the handles with `--json`, pass them all to `marker info --json`, and extract `cpuPercent` with a script.
- What the output could have shown: the `CPU Use` percentage in the list row's label, or `counter`-style buckets built from those markers.

## Question: the order of markers that fall within the same millisecond

- Command: `profiler-cli thread markers --list` (for example `--search 'name:Runnable'` zoomed to 6.06,6.21 in profile_browser_confirmFolderUpload.js.json of task SavkcSEVTlmGVk-z--wzmA).
- Expected: timestamps precise enough to order a timer callback against a test assertion logged in the same millisecond.
- Got: `t=6.199s` for the TimeoutExecutor runnable, the TEST-UNEXPECTED-FAIL and the `startOnFocusDelay` runnable alike. Settling which ran first took `--json` and a script to print `start` to 0.001 ms.
- What the output could have shown: sub-millisecond start times in list mode once the zoom range is below about a second, or a `--precise` flag.

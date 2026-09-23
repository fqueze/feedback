## `thread markers --list` timestamps are rounded to whole seconds, even when zoomed into 1 s

- Command: `profiler-cli zoom push 405.5,406.62 --session X; profiler-cli thread markers --category DOM --list --limit 0 --session X`
- Question: in which order, and how many ms apart, did the test's `info()` log, the pointerdown/mousedown/click DOMEvents and the focus changes happen within one second?
- Expected: a time column precise enough to order and space the rows of the view (ms, or seconds with 3 decimals, relative to the profile start), at least once the view is shorter than a few seconds.
- Got: every row reads `t=6m46s` or `t=6m47s` in an 8m15s profile; the rows within one second cannot be ordered against rows of another query, nor spaced.
- Workaround: `--json` and read `flatMarkers[].start` with a script.

## `thread markers` "Frequency Analysis" reports thousands of markers/sec for 14 markers in an 88 s view

- Command: `profiler-cli zoom push 406.5,494.7 --session X; profiler-cli thread markers --thread t-3 --session X`
- Question: was the Compositor thread active while the test waited?
- Expected: a rate over the view (14 IPCIn markers in 88 s is about 0.16/s).
- Got: `IPCIn: 31183.8 markers/sec (interval: min=23.001μs, avg=34.535μs, ...)`, apparently the rate over the span between the first and last marker, which here is a burst of a few hundred µs. It reads as a busy thread when the thread was all but idle.
- Workaround: read the marker count in "By Name" and divide by the view length myself.

## Machine CPU over a window of a resource-usage profile (review-browser_dbg-pause-on-next.js)

- Question: what was the machine's average CPU during this test's wait, and in the 1.5 s around the hang's onset?
- Command: `profiler-cli counter list --session X` on `profile_resource-usage.json`; then `profiler-cli zoom push 432.19,551.85; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`
- Expected: a CPU-over-time summary for the view, the kind `counter info` gives, or `thread info`'s "CPU activity over time".
- Got: "No counters in this profile", and `thread info` shows "No significant activity". Machine CPU is only in the ~1,200 `CPU Use` markers per 2 minutes, each with a `cpuPercent` field, and the default output neither lists nor averages them.
- Workaround: `--json`, then average `fields[cpuPercent]` with a script.

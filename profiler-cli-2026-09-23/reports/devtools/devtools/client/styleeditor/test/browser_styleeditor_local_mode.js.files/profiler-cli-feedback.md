# profiler-cli feedback (browser_styleeditor_local_mode.js)

## Question: "what were the machine's CPU, iowait and disk writes during this 500 ms window of a resource-usage profile?"

- Command: `profiler-cli profile info --session …-2` and `profiler-cli counter list --session …-2` on `profile_resource-usage.json`.
- Expected: CPU / IO tracks, as the profiler UI shows them.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data exists as `CPU Use` / `IO` markers every 100 ms (`cpuPercent`, `iowait_pct`, `write_bytes`), but `thread markers --search "name:CPU Use,name:IO" --list` prints only the name for each one, with no field values.
- Workaround: a Python script over `thread markers --json` to table cpu/iowait/written bytes per 100 ms, and a second one to find runs of sustained writes in the whole job.
- What the output could show: for resource-usage profiles, `profile info` (or `counter list`) summarising CPU, iowait and disk writes over the current zoom, or `--list` printing the key fields of `CPU`/`IO` markers.

## Question: "which time in the resource-usage profile matches t in the per-test profile?"

- Both profiles have the same `INFO` lines on different time bases. I worked out the offset by hand (26043.9 ms) from two matching INFO markers, to place a `C++ warning` from the job log against a per-test marker.
- What the output could show: an option to express times relative to a named marker (for example `--relative-to "Entering test"`), so two profiles of the same test can be lined up.

## Question (review): "at what ms did each marker in this 30 ms window start?"

- Command: `profiler-cli zoom push 119.130,119.160 --session review-…-1` then `profiler-cli thread markers --list --limit 0 --session review-…-1`.
- Expected: each marker's start in ms, precise enough to order markers inside a 30 ms window.
- Got: every row prints `t=1m59s` (whole seconds), and `marker info` does the same (`Time: 2m`). You cannot order the rows, or check a report's 119641.3 vs 119642.6, without the `--json`.
- Workaround: a Python script over `thread markers --list --json` that prints `start` and `duration` in ms.
- What the output could show: a start in ms (for example `119641.3ms`), or a precision that follows the zoom width.

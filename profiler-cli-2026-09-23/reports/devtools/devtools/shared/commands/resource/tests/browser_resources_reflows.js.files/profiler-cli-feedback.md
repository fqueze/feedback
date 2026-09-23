## Was the machine saturated during this test? (resource-usage profile)
- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --session <s>` after `zoom push m-<test marker>`
- Expected: each CPU Use row showing its CPU percent (or a min/mean/max summary for the zoomed range).
- Got: 40 rows reading only `CPU Use  t=22m7s  101ms`, no value. Had to `marker info` one, then `--json` + a python script to get min/mean/max of `data.cpuPercent`.
- Could have shown: the `CPU Percent` field inline in the list row, or aggregated stats in the non-`--list` summary.

## Precise times of markers after the first minute
- Command: `profiler-cli thread markers --search "Reflow (,setTimeout" --list --limit 0` on a profile where the test ran at t≈70s.
- Expected: millisecond times, as for t<60s (`t=48.817s`).
- Got: `t=1m10s`, `t=1m11s` — one-second resolution, useless for correlating a 300 ms timer with a reflow. Workaround: `--json` and print `start/1000` with a script.
- Could have shown: `t=70.595s` (or `1m10.595s`).

## Which process sent each of these IPC messages?
- Command: `profiler-cli thread markers --search "Runnable,IPCIn" --list --limit 0` on the parent main thread, zoomed to 80 ms between two waitFor polls.
- Expected: the IPCIn row naming the sender (`from https://example.com (pid 5313)`) and message type.
- Got: bare `IPCIn  t=2.901s  43.005μs`; had to `marker info` each handle to read `From:`.
- Could have shown: the `niceDirection`/`From` field in the list row, as `marker info`'s header already does.

## Was the sampler itself sampling on schedule? (review-browser_resources_reflows.js)
- Command: `profiler-cli thread samples --include-idle` after `zoom push 2.70,3.46`, and `profile meta`.
- Expected: the effective sample interval next to the configured one, since late timers were the question.
- Got: `Sampling interval: 10ms` in `meta`, and only a "14 running samples" count in `samples`. I had to divide that by the range by hand to get about 55 ms between samples, then repeat it on two more threads.
- Could have shown: `profile info` or `thread samples` could print "N samples, mean gap X ms (configured 10 ms)" for the zoomed range, and flag a large mismatch.

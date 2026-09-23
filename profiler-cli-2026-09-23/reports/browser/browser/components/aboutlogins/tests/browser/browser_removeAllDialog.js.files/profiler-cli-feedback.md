## Question: "was the machine I/O-bound (iowait, disk writes) while this step stalled?"

- Command: `profiler-cli thread markers --search "name:CPU Use,name:IO" --list --limit 0 --session <s>` on a job's `profile_resource-usage.json`, zoomed to a 1.6 s window.
- Expected: each row showing its values (CPU %, IO wait %, write bytes/s), or a counter I could `counter info` over a zoom.
- Got: rows reading only `CPU Use  t=1m18s  100ms` / `IO  t=1m18s  100ms` with no values; `counter list` says "No counters in this profile" and `profile info` says "CPU activity over time: No significant activity", although the markers carry `cpuPercent`, `iowait_pct`, `write_bytes`. Also the list prints `t=1m18s` for every row in a window, so 100 ms markers cannot be told apart by time.
- Workaround: `--json` and a Python script bucketing `data.write_bytes` and the `iowait_pct` field per second. It showed a 13–16 s burst of ~169 MB/s machine-wide writes with 25–68% iowait covering the failure in all three jobs read — the key observation for this report, and invisible in the default output.
- What would have answered it: `counter info` (with its "over time" buckets) over resource-usage CPU/IO/Memory data, or the marker's main fields in the `--list` label; and sub-second timestamps in `--list` when zoomed under a few seconds.

## Question (review): "which IndexedDB transactions/requests were in flight on this thread during this wait?"

- Command: `profiler-cli thread markers --search IDB --list --limit 0 --session <s>` zoomed to 50.6–52.9 s.
- Expected: each IPC row showing its message type (e.g. `PBackgroundIDBTransaction::Msg_PBackgroundIDBRequestConstructor`), since the search matched on it.
- Got: rows labelled only `IPCOut` / `IPCIn` with no message type (only `Runnable` rows carried it). 129 rows could not be told apart without `marker info` on each.
- Workaround: `--json` plus a script printing `fields[messageType]` per row, deduplicated by time.
- What would have answered it: the IPC marker's message type (and direction) in the `--list` label.

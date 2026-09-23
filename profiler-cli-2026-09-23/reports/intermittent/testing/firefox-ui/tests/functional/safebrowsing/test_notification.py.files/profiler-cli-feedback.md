## Machine network I/O around a timestamp needs a script over `--json`
- Question: "how many bytes did the machine send/receive in the second after the click at t=19.51s, compared with a passing job" (resource-usage profile, `NetIO` markers every ~100 ms).
- Command: `profiler-cli thread markers --session <s> --search name:NetIO --list --limit 0` (after `zoom push 16.5,21`)
- Expected: the Sent/Received fields per row, or a per-bucket sum, so the burst (or its absence) is visible.
- Got: rows with only name, time and duration; the payload needs `marker info` per handle.
- Workaround: `--json` dump, then Python to bin `data.sent_bytes/recv_bytes` per second and per window around log timestamps, over 90 tasks. A `--sum-field` / per-interval aggregation of numeric marker fields (or printing numeric payload fields in `--list`) would have answered it directly.

## Question: which IPC messages (by type) went to and from one process, in order, over a time range?

- Command: `profiler-cli thread markers --search "messageType:IDB,messageType:Cache,..." --list --limit 0`
- Expected: a chronological list showing each IPC marker's message type, sender and receiver (the `Type`, `From`, `To` fields).
- Got: rows reading only `IPCIn` / `IPCOut` with a time and duration; the message type is only visible via `marker info` one handle at a time, or aggregated with `--group-by field:messageType` (no times).
- Workaround: `--list --json` and a script printing `fields[messageType]`, `sendThreadName`, `recvThreadName` per marker. The IPC timeline (request at 44.590, reply at 52.643) was the core evidence of this report.

## Question: was the machine's disk saturated while the test stalled?

- Command: `profiler-cli profile info` / `profiler-cli counter list` on a `profile_resource-usage.json`
- Expected: a summary of the machine's CPU, iowait and disk-write rate over time, like the `CPU activity over time` section of Firefox profiles.
- Got: `No significant activity.` and `No counters in this profile.`, although the profile carries `CPU Use` (with `iowait_pct`) and `IO` (with `write_bytes`) markers every 100 ms.
- Workaround: `thread markers --search "name:IO,name:CPU Use" --list --limit 0 --json` and a script aggregating per second. That showed a ~2.5 GB write stream at ~168 MB/s with 20-34% iowait, which the default output never hinted at.

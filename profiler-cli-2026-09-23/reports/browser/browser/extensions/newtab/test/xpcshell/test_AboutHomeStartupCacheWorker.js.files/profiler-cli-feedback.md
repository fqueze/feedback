## Question: what was the machine's CPU use while this test ran (resource-usage profile)?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <ru>` after `zoom push m-1` (the test's `test` marker).
- Expected: each `CPU Use` row showing its CPU percent (and idle %), since that value is the only reason to list them.
- Got: only start time and duration per row; the percentage needed `marker info <handles> --json` piped through a script.
- Also: `counter list` says "No counters in this profile" and `profile info` says "No significant activity", which reads as "no CPU data" although the CPU Use markers hold it.
- Workaround: `marker info m-339 m-330 ... --json` and read `fields[].cpuPercent` / `idle_pct`.

(Second occurrence, same question, job Fm2ioDLKRq-0U2RbmwOtNA: comparing CPU during the failing run and the passing retry needed `thread markers --search "name:CPU Use" --list --json` to collect handles, then `marker info <all handles> --json` and a script to print `cpuPercent`/`idle_pct` per row. A `--fields cpuPercent,idle_pct` on `--list`, or showing the Text/fields summary column for these markers, would have answered it in one command.)

## Question: where did one IPC message's latency go — sender I/O thread, transfer, or the receiver's event queue? (review)

- Command: `profiler-cli marker info m-757 --session <id>` on an `IPCOut` of `PWindowGlobal::Msg_RawMessage` (66 ms).
- Expected: the phases as profile-relative times or as durations — "sender endpoint 3.985 s, sender I/O done 4.041 s (+56 ms), receiver I/O 4.042 s, receiver endpoint 4.051 s (+9.5 ms queued)".
- Got: `Time: 3.985s - 4.051s`, then under "Other payload fields (no schema)" raw `startTime 4049.42`, `sendStartTime`, `sendEndTime`, `recvEndTime`, `endTime` in another time base (offset by the profile's zero, equal to `profile meta` "Uptime" 64.494 ms), unlabelled. Mapping them needed `--json` and a script subtracting the offset.
- Also: for parent-to-child messages `sendStartTime`/`sendEndTime` came out later than `recvEndTime` (e.g. m-768: recvEnd 3984.45, sendStart 3985.16 after the offset), so either the phases are mis-correlated or the fields mean something else; the output gives no way to tell.
- Workaround: `marker info <m> --json`, read `rawFields`, subtract `profile meta` Uptime.

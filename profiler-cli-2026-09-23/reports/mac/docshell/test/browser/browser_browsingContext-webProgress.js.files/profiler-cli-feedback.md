## Question: which IPC message is each IPCIn/IPCOut marker in a list?

- Command: `profiler-cli thread markers --search StateChange --list --limit 0 --session bcwp-1`
- Expected: each IPC row to show its `messageType` (e.g. `PBrowser::Msg_OnStateChange`) and the other side (process name / pid), as the profiler UI's marker table does.
- Got: rows read only `m-259  IPCIn  t=17.849s  116.07μs  ✗` — the search matched on the message type, but the row does not say which of several matching types it is, nor the peer process. Needed `marker info` per handle or `--json` + a script (`flatMarkers[].data.messageType`, `sendThreadName`, `recvThreadName`).
- Workaround: a Python script over `--list --json`.

## Question: how busy was the machine when this test failed (resource-usage profile)?

- Command: `profiler-cli profile info --session bcwp-ru3` and `profiler-cli counter list --session bcwp-ru3` on `ZsQSmqVkQ56Y1b2qPN-JqQ/.../profile_resource-usage.json`
- Expected: a machine CPU track (the brief calls it "the CPU use of the whole machine").
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is in 14,932 `CPU Use` interval markers (`cpuPercent`, `idle_pct`), which neither command mentions.
- Workaround: `zoom push <range>` then `thread markers --search "name:CPU Use" --list --json` and a script to print `fields`. `profile info` could summarise the `CPU Use` markers the way it does CPU samples.

## Question (review): where was this IPC message delayed: queued on the sender, in transfer, or waiting on the receiver's main thread?

- Command: `profiler-cli marker info m-2 --session review-bcwp-3` (the `PContent::Msg_LoadURI` IPCOut in ZsQSmqVkQ56Y1b2qPN-JqQ)
- Expected: the per-phase times (sender endpoint, IO TransferStart, TransferEnd, receiver TransferEnd, receiver endpoint) on the same clock as `Time:`, or as per-phase durations.
- Got: `Time: 1m39s - 1m39s (28.496ms)` plus raw `startTime`, `sendStartTime`, `recvEndTime` and `endTime` under "Other payload fields (no schema)". Those are on a clock offset from the marker's own start: by 26.367 ms in this profile and 8.1 ms in OtI9qAmxSVWoR. So they can't be compared with other markers until you subtract the offset.
- Workaround: `--json` and a script computing `offset = rawFields.startTime - start`, then `sendStartTime - startTime` and so on for each IPC marker in the range. That script is how I found the parent's I/O thread stalled 30 ms across every channel. A `thread markers --search IPC --list` column with "queued / transfer / receiver wait" would have shown it directly.

## Question (review): open a report's link to check it, then build a corrected link

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "<report's profiler.firefox.com/from-url link>" --session review-bcwp-2` (as review-brief.md says), then `python3 $D/profile-link.py --session review-bcwp-2 --marker m-1 --range 32.295,32.340`
- Expected: a link.
- Got: profile-link.py refuses sessions whose `profilePath` is a profiler.firefox.com URL ("A link needs the raw artifact URL"), although the URL embeds it (`from-url/<encoded Taskcluster URL>`).
- Workaround: stopped the session and reloaded the same profile from the raw Taskcluster URL, which cost a second 0.6 GB load. profile-link.py could decode the `from-url` path.

## Setup: default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session review-bcwp-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` worked. The briefs don't mention the variable, so every agent pays one failed call to find it.

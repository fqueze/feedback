# profiler-cli feedback (browser_stop_sharing_button.js)

## Question: which IPC messages (by type) were sent/received, and did request X ever get its reply?

- Command: `profiler-cli thread markers --session <s> --search PCameras --list --limit 0`
- Expected: each IPCIn/IPCOut row showing its message type (e.g. `PCameras::Msg_AllocateCapture`), direction and other pid, so a missing reply can be spotted by reading the list.
- Got: rows show only `IPCIn` / `IPCOut`, duration, and no payload text at all, so 23,000 IPC rows are indistinguishable. Had to `marker info` individual handles, then fall back to `--list --json` plus a Python script to tabulate `data.messageType`, `data.messageSeqno`, `data.side`, `data.otherPid` and count messages per type after a timestamp.
- Workaround: `--json` + script.
- What the default output could show: the IPC marker's message type and "sent to / received from <process>" (the `niceDirection` already present in the payload) as the row's description, like Runnable rows show their task name.

## Question: was the machine saturated while this test hung? (resource-usage profile)

- Commands: `profiler-cli profile info` and `profiler-cli counter list` on
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/MYLE8O4QTeiUb2Zf5SFnyw/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: machine CPU over time (like "CPU activity over time" for Gecko profiles), or at least over a zoomed range such as the test's own `test` marker.
- Got: `profile info` says "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile." The CPU data is there, as ~3,700 `CPU Use` markers (one per 100 ms, `cpuPercent` field), but nothing summarizes them.
- Workaround: `zoom push m-1` (the test marker), `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` per 30 s.
- What the default output could show: for mozharness resource-usage profiles, `profile info` (respecting zoom) could print mean/max machine CPU per time bucket from the `CPU Use` markers.

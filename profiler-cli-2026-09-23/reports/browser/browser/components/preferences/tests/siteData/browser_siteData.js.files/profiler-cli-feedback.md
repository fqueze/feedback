## Which IPC messages did this thread send/receive, and when?
- Command: `profiler-cli thread markers --session S --search messageType:PCache --list --limit 0`
- Expected: each row showing the IPC message type (e.g. `PCacheStorage::Msg_PCacheOpConstructor`), direction and phase.
- Got: rows labelled only `IPCOut` / `IPCIn` with an empty description, so the list cannot say which message is which.
- Workaround: collected handles with `--json`, ran `marker info <handles…> --json` and a Python script to print start, duration, `messageType`, `direction`, `phase`. The default list could show `messageType` and `niceDirection` as the description.

## When did this network request start?
- Command: `profiler-cli thread network --session S --search service_worker_test.js --sort start`
- Expected: a start time per request (the list is even sorted by start).
- Got: duration and phases only; no start time.
- Workaround: `marker info m-N` per request.

## Marker times past 60 s lose their precision
- Command: `profiler-cli thread markers --category Test --list` on a 61 s profile; `marker info m-9` on a 21 min profile.
- Expected: `t=59.302s`, `t=1m0.412s`, or plain seconds with ms.
- Got: `t=1m` for every marker between 60 s and 61 s (dozens of rows), `Time: 1m22s (instant)` in marker info. Events cannot be ordered or aligned with another profile past the first minute.
- Workaround: `--json` and divide `start` by 1000.

## Was the machine's disk saturated during this window? (resource-usage profile)
- Command: `profiler-cli profile info` / `counter list` on `profile_resource-usage.json`.
- Expected: a summary of machine CPU (user/iowait/idle) and disk write/read throughput over time or over the zoomed range, like the counter sparkline.
- Got: "No significant activity" and "No counters in this profile"; the data exist only as 12,745 `CPU Use` / `IO` interval markers.
- Workaround: dumped every `IO` marker with `marker info --json` (12 MB) and bucketed `write_bytes` per second in Python to find a 2.6 GB write burst. A `counter`-like view over these markers would have answered it in one command.

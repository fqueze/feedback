## Precise times of the test's log markers (8m58s profile)

- Question: at what sub-second time did each `INFO`/`TEST-*` marker, and each `Msg_Register`/`AsyncOpen` marker, happen, so I can order them against each other?
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_dbg-features-source-tree.js --list --limit 0`
- Expected: times with ms precision, as they are for short profiles.
- Got: `t=7m51s` for every row. The 20 test-server lines spread over 1.5s, and a registration 40ms before a fetch, could not be ordered. `marker info` also prints `Time: 7m51s`. Zooming to a 57s range did not change the format.
- Workaround: `--json` piped into a Python script printing `start/1000` with 4 decimals. The default output could have used ms precision, or a fixed seconds format, whenever the gaps between listed rows are under a second.

## Which content process was alive during the failing subtest

- Question: which `http://localhost` process hosted the page during t=468s to 525s?
- Command: `profiler-cli profile info --all --session <s>`
- Expected: process start and end times in seconds.
- Got: opaque handles like `[ts-YQ → ts-YY]`. I resolved each one with `zoom push ts-YQ,ts-YY` followed by `zoom pop`, six round trips. Separately, `Process CPU [0 samples]` under a zoom was the only hint that none of the localhost processes were alive in that range. The one that was (pid 5300, seen only in `Subprocess Priority` markers) is missing from the profile, and nothing says so.
- Workaround: the zoom push/pop loop. Printing the times next to the handles would have answered it directly. Flagging pids that parent markers refer to but that are absent from the profile would have saved a search.

## (review) Which thread sent an IPC marker filed on GeckoMain
- Question: did the content main thread send `PRemoteWorkerDebuggerManager::Msg_Register`, or another thread?
- Command: `profiler-cli thread markers --session <s> --search PRemoteWorkerDebugger --list` on t-20, then `marker info`
- Expected: the sending thread, since the marker is listed on t-20 as `IPCOut`.
- Got: an `IPCOut` row on t-20. Only the unschema'd `phase: transferStart`, `side: unknown`, `threadId: 800` fields show that an unprofiled thread (Worker Launcher) sent it. The report's author took it as t-20 sending.
- Workaround: `--json` and read `threadId`/`phase`. The default output could say "sent by thread 800 (not profiled)" for these markers.

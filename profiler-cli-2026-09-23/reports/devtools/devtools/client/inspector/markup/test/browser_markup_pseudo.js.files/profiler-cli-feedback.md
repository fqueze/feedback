## Question: at what time, to the millisecond, did each test log line happen?

- Command: `profiler-cli thread markers --category Test --search browser_markup_pseudo.js --list --limit 0 --session ...` and `profiler-cli marker info m-58 m-87 m-16 --session ...`
- Expected: timestamps precise enough to order events within a second (e.g. `5m39.412s`), since a whole subtest ran in 1 s.
- Got: `t=5m39s` for every line, in both the list and `marker info` ("Time: 5m39s (instant)"). With profiles over a minute long, sub-second order and gaps between log lines are lost.
- Workaround: `--json` and reading `start` (ms) with a script.

## review-browser_markup_pseudo.js — Question: when did this IPC message reach the content process, on the same clock as the other markers?

- Command: `profiler-cli marker info m-49 --session ...` (an `IPCIn` marker)
- Expected: `sendStartTime`/`recvEndTime` on the same zero as the marker list, so they can be compared with the `TaskController::AddTask` marker next to them.
- Got: raw payload values (`recvEndTime: 339504.49`), 22.3 ms off from the marker's own `start` (339482.0 on the list clock). These fields are "no schema", so they get no conversion, and the offset is not shown anywhere.
- Workaround: `--json`, then subtract `data.startTime - start` from each field.

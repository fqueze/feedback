## Question: in what order did events a few ms apart happen (IPC round vs. test log)?
- Command: `profiler-cli thread markers --session S --search messageType:PContent::Msg_RawMessage --list --limit 0`
- Expected: each row with a millisecond start time, the peer process, and the send / handled times.
- Got: `t=3m25s` for every row (rounded to the second in a 3-minute profile), with no peer and no message type. That cannot order a query round, its replies and a test INFO line that are 1–50 ms apart.
- Workaround: `--json` piped through python to print `data.sendStartTime`, `data.endTime`, `recvThreadName`/`sendThreadName` and `messageSeqno`. Showing ms precision when rows are this close, plus the IPC peer and message type in `--list`, would have answered it.

## Question: at what time was function X on the stack (e.g. `purgeRequests`, `local-transport.js!close`)?
- Command: `profiler-cli thread functions --search devtools-client.js` gave totals only, with no time.
- Workaround: a shell loop doing `zoom push` over 20 ms windows and grepping `thread functions` in each: about 50 round trips.
- Would have helped: the first and last sample times of a function, e.g. `function info f-N` printing "sampled at 205.091–205.108 s" or a `--timeline` view.

## IPC payload times are in a different time base from the marker times on a Windows profile
- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LK9asePWTquN06XfU8Z42A/runs/0/artifacts/public/test_info/profile_browser_rules_color_scheme_emulation.js.json
- Command: `profiler-cli marker info m-167 --session S` (an IPCOut on the parent main thread)
- Expected: the marker's start to match its payload `startTime`, as it does on Linux profiles (for example m-183 in ZWEoRBskSbKWdK-J1vJR-g: 205064.56 in both).
- Got: marker start 233804.39 but payload `startTime` 233829.74 and `sendStartTime` 233829.75, a 25.35 ms offset. Every IPC marker in that profile has the same offset. Comparing payload times with the test's INFO markers put a reply 25 ms on the wrong side of the test continuing.
- Workaround: use only marker start/end. It would help to flag or rebase payload times that are not in the thread's time base.

# profiler-cli feedback — test_img_src_causing_reflow.html

## Question: "what did the parent exchange with pid X, in order?" (X absent from the profile)

- Command: `profiler-cli thread markers --category IPC --search "otherPid:3310" --list --limit 0 --session <s>`
- Expected: one row per message, with its message type, direction and the other pid, e.g. `IPCIn PBrowserBridge::Msg_Show  received from 3083`.
- Got: every row reads `IPCIn` or `IPCOut` and nothing else (`m-9411 IPCIn t=55.953s 0s ✗`). The message type, which is the whole point of an IPC marker, is not on the row.
- Workaround: `--json`, then a script that prints `data.messageType`, `data.direction` and `data.otherPid` per marker, and dedupes the phases (endpoint / transferStart / transferEnd) of one message by `messageSeqno`.
- What the default output could have shown: `messageType` and `niceDirection` on each IPC row, as the tooltip label already does in `marker info` ("IPC — received from 3083").

## Question: "when did this message reach the parent, as opposed to when the main thread ran it?"

- Command: `profiler-cli marker info m-12147 --session <s>`
- Got: `Time: 55.954s - 55.954s (0s)`, then under "Other payload fields (no schema)": `endTime: 55962.686125`, `recvEndTime: 55945.931458`. Those are raw milliseconds in another time base, about 8.2 ms off the profile-relative times printed everywhere else. The offset has to be worked out by hand, from a sending marker's `startTime` against its displayed start.
- Expected: `recvEndTime` (I/O-thread arrival) converted to profile time, e.g. `Arrived: 55.9377s (16.8 ms before the main thread handled it)`. That queueing delay was the key observation here: the harness's reply arrived 26 ms after the request, and then waited 17 ms more behind a busy main thread.
- Workaround: a script that computes the offset from the endpoint `sending` markers and subtracts it.

## Question: "which content process ran the test at t=55.9s?"

- Command: `profiler-cli profile info --session <s>` (and `--all`)
- Got: processes sorted by CPU, with their lifetimes as `ts-k → ts-R` handles. The last content process in the profile had ended 1.4 s before the test ran, so the test's own process was not in the profile. Finding that out took `profile info --all --json` and a sort by `endTime`.
- Expected: either real times next to the lifetimes, or a note in `profile info` like "no content process covers t=54.5s → end". A per-test profile whose test process is missing looks like any other until you go looking for its markers.

## Question: "how long did each MakeFrameRemote take to get its answer, over the whole profile?"

- Command: `thread markers --search "messageType:MakeFrameRemote" --list --limit 0 --json` and the same for `PBrowserBridge::Msg_Show`, then a script that pairs each request with the next Show.
- Expected: some way to pair a request with its reply (`Msg_X` → `Reply_X` with the same seqno) and print the latency distribution, since the reply seqno is already in the payload.

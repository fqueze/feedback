## When exactly did each step happen (sub-second)?
- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` (and every other `--list`)
- Expected: times precise enough to order events that happen within one second (ms).
- Got: `t=3m2s` for every row in a 450 ms window; ordering the network/IPC/test steps took `--json` and a python script over `.flatMarkers[].start`.
- Could have shown: `t=182.303s` (ms precision) once the profile is longer than a minute, or at least inside a zoom narrower than a few seconds.

## Which IPC messages went to which process?
- Command: `profiler-cli thread markers --search 'IPCOut,IPCIn' --list --limit 0`
- Expected: the message type and the other side, e.g. `PDocumentChannel::Msg_RedirectToRealChannel -> Web Content (5/5)`.
- Got: every row is just `IPCOut` / `IPCIn` with no description; had to script over `--json` `fields[messageType,sendThreadName,recvThreadName]`.
- Could have shown: messageType and the peer process in the list's description column.

## Network payload times vs marker times
- Command: `profiler-cli marker info m-52 --json` / `thread network --json`
- Got: marker `start` is profile-relative (182264.87) while rawFields `startTime/requestStart/responseEnd` are raw (182275.14, offset by the profile's zero, 10.27 ms here), with no note. Easy to mis-order steps by 10 ms when mixing them.
- Could have shown: the payload timestamps converted to the same time base as `start`, or labelled as raw.

## Response fields of a network request in the default output
- Command: `profiler-cli thread network --sort start`
- Got: `???  cache=Unresolved  duration=169.14ms` for the failed request, and no httpVersion/responseStatus/requestStatus for any request; had to `marker info --json` each one to compare a failed load (NS_ERROR_ABORT, no response) against successful ones (h3, 200).
- Could have shown: requestStatus, httpVersion and responseStatus per row.

## Process start times in `profile info --json`
- Command: `profiler-cli profile info --all --json | jq '.processes[] | {name,startTime,endTime}'`
- Got: `startTime` is 1116.9 ms for every one of the 56 processes, including ones launched minutes in; endTime looked right.
- Workaround: ignored startTime; used network markers per thread instead.

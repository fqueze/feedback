## `thread markers --search bfcache --list`: IPC rows match but show no message name

- Command: `profiler-cli thread markers --thread t-0 --search bfcache --list --limit 0 --session 2043133.nofis-subtest3-timeout-1`
- Expected: the matching rows say why they matched, as `Runnable` rows do (`PWindowGlobal::Msg_UpdateBFCacheStatus - priority: ...`).
- Got: dozens of bare `IPCOut` / `IPCIn` rows with an empty description column (they match on their `messageType` field, which the list does not print), and 30+ `Preference Read` rows for `docshell.shistory.bfcache.*` prefs. The one marker I needed, `BFCache not cached ... blocked by REQUEST`, was buried among them.
- Workaround: `--search "-name:IPCOut,-name:IPCIn,-name:Preference Read"` on a zoomed range, then grep.
- Could show: the IPC message type in the description of IPC markers in `--list`.

## (review) IPC marker JSON mixes two time bases in one record

- Command: `profiler-cli thread markers --search IPC --list --limit 0 --json --session review-2043133.nofis-subtest3-timeout-1` (zoomed on 23.70-23.80)
- Question: when did the child's `PDocumentChannelConstructor` reach the parent's IPC I/O thread, compared with when the parent main thread ran `nsHttpChannel::OnStartRequest`?
- Got: each flat marker's `start` is relative to the profile's root range, while its `data.endTime`, `data.recvEndTime`, `data.sendStartTime` are absolute, 40.911 ms apart here. `context.currentViewRange` is absolute too. Nothing says which is which; I had to find the offset by subtracting `start` from `endTime` of a receiving endpoint.
- Could show: all IPC phase times in the same base as `start`, and the I/O-thread receive time (`recvEndTime`) in the text `--list` and `marker info` output, since it is what tells "arrived late" from "arrived early, handled late".

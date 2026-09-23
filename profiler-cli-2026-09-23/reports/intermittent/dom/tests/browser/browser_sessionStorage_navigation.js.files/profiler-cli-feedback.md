## IPC markers in `thread markers --list`: no message type in the default output

Question: "which IPC messages did this content process send/receive around t, and when were they handled?"

- Command: `profiler-cli thread markers --session <s> --search SessionStorage --list --limit 0`
- Expected: each IPCIn/IPCOut/SyncIPCOut row naming its `messageType` (e.g. `PBackgroundSessionStorageCache::Msg_Load`) and the other pid.
- Got: rows like `m-2632  IPCOut  t=2m5s  -1,158,070ns  ✗` with no message type, no pid, and a negative duration (clock skew between processes). Only the rare `PBackgroundSessionStorageCache::Msg_Load` marker (a separate label marker) was self-describing.
- Workaround: `--json` and a Python script printing `data.messageType`, `data.otherPid`, `data.direction`.
- What would have answered it: show `messageType` and `otherPid` (and direction) in the description column for IPC markers, as is done for Runnable markers.

## IPC payload times are in a different time base from the marker start

Question: "did the parent handle message X before or after event Y?" (the ordering that decided this failure).

- Command: `profiler-cli thread markers ... --search SetSingleChannelId --list --json`
- Expected: `data.startTime/endTime/sendStartTime/recvEndTime` in the same zero as `start` (profile-relative ms).
- Got: payload times offset by ~6.84–6.88 ms from `start` (e.g. `start` 116083.72 vs `data.sendStartTime` 116090.56); the handling time (`data.endTime`) had to be shifted by hand to match the `Runnable PWindowGlobal::Msg_SetSingleChannelId` marker. Also the displayed `start` of an IPCIn marker is the *sender's* send time, in the sender's clock, which can be 1–3 ms off the receiver's on Windows, so the list order is not the handling order.
- Workaround: subtract the offset computed from an IPCOut marker, and match `endTime` to the receiver's Runnable markers.
- What would have answered it: for IPC markers, print the receive/handled time on the receiving thread, in profile-relative time, next to the send time.

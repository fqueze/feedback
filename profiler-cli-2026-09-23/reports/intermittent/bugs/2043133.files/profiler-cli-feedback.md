## IPC marker list does not say which message or which peer

- Question: "which IPC messages did the parent exchange with content pid 3104, in send order?" (to compare a crashing navigation with passing ones).
- Command: `profiler-cli thread markers --category IPC --search "LoadURI,DeactivateDocuments,..." --list --limit 0 --session 2043133-1`
- Expected: one row per message with its `messageType`, direction, peer pid and `messageSeqno`.
- Got: rows reading only `IPCOut` / `IPCIn` with a time and duration; the message type, peer and seqno are only in `marker info` or `--json`. `--search` does match the message type, so filtering works, but the listed rows cannot be told apart.
- Workaround: `--json` and a Python script printing `data.messageType`, `data.otherPid`, `data.messageSeqno`. The seqno turned out to be the key evidence (child send order on the PContent channel), so showing it in the list would have answered the question directly.

## IPC marker raw time fields are not in the profile-relative time base (review-2043133)

- Question: "when did the parent's IO thread receive 3104's `__delete__`, compared with when the parent sent `ReactivateDocuments`?" (the child crashed, so only the parent side exists).
- Command: `profiler-cli marker info m-233 m-241 --session review-2043133-1 --json`
- Expected: `sendStartTime`, `recvEndTime`, `startTime` and `endTime` in the same profile-relative ms as `start`.
- Got: `start` 26980.708 but `endTime` 27004.887 and `recvEndTime` 26989.787. The raw fields are offset by the profile's zero (24.18 ms here, 33.61 ms on the Windows profile), so `recvEndTime` looks later than the main-thread handling, and IO-thread timings can't be read directly.
- Workaround: take the offset from an outgoing marker (`sendStartTime - start`) and subtract it by hand in a script.

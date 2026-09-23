## Question: when did this IPC message reach the receiving process, relative to other markers on its main thread?

- Command: `profiler-cli marker info m-59 --session S` (an `IPCIn` marker, `PWindowGlobal::Msg_UpdateDocumentTitle`)
- Expected: the transfer phases (sent by sender main thread, IO send end, IO receive end, main-thread processing) in the same time base as `t=` everywhere else, with µs precision.
- Got: `Time: 46.653s - 46.662s (8.811ms)` at ms precision, and the phase times only as raw "Other payload fields" (`startTime: 46660.55`, `recvEndTime: 46660.7497`...) in a different time base (offset ~7.78 ms from the marker's `start`), unlabelled as such.
- Workaround: `marker info ... --json`, then a script computing `offset = rawFields.startTime - start` and converting `sendEndTime`/`recvEndTime` into profile time, to compare a 12 µs race against a `SendQueryReply` marker. The IPC marker text output could print the phases converted to profile time (µs precision), and the list view could show µs when two markers share the same ms.

## Question (review): when did a lazily-sent IPC message actually leave the sender?

- Command: `profiler-cli marker info m-966 --session S` (`IPCOut PWindowGlobal::Msg_UpdateDocumentTitle`, a `[LazySend]` message)
- Expected: the phases in profile time, so the 24 µs gap between the `Send` call (marker start) and the IO thread picking the message up (`sendStartTime`) is visible.
- Got: only the marker start (the `Send` call), at ms precision. The later phases only appear as raw fields in another time base, so the report quoted the `Send` time as the send time.
- Workaround: `--json` plus the same offset script as above.

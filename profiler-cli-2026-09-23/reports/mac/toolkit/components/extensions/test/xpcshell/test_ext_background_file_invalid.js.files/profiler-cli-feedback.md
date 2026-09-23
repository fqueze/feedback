## Question: which IPC message is each IPCIn/IPCOut marker, and between which processes?

- Command: `profiler-cli thread markers --search "name:IPCIn" --list --limit 0 --session <s>` (also for `IPCOut`).
- Expected: each row showing the payload's `messageType` (e.g. `PNecko::Msg_PDocumentChannelConstructor`) and the sender -> receiver, the way `Runnable` rows show their task name.
- Got: rows with an empty description (`m-2301  IPCIn  t=1.093s  71.27ms  ✗`), so a list of 100 IPC markers says nothing without `marker info` on each one.
- Workaround: `--json` piped through a Python script printing `fields.messageType`, `sendThreadName`, `recvThreadName`. Needed three times in this investigation (the error page's DocumentChannel constructor wait, the order of `CrossProcessRedirect` vs `RedirectToRealChannel`).

# profiler-cli feedback (browser_taskbarTabs_chromeTest.js)

## Question: which IPC messages did this content process receive, and when did it actually receive them?

- Command: `profiler-cli thread markers --search "PSessionStore" --list --limit 0 --session S` (and the same with `profile markers`).
- Expected: each IPCIn/IPCOut row to show its message type (e.g. `PContent::Msg_ConstructBrowser`) and the send and receive times.
- Got: IPCIn/IPCOut rows with an empty description column, and on the receiving thread a start/duration spanning parent send to child processing (e.g. `t=16.706s 8.956s`), with no way to tell send time from receive time in the list. The `Runnable` rows do carry the message name, so in the list only main-thread-dispatched messages are identifiable.
- Workaround: `profile markers --thread t-7 --search ... --json` and a script to print `data.messageType`, `start` and `data.recvEndTime`.
- What would have answered it: the message type in the IPC row's description, plus "sent t=X, received t=Y" (or the phase times) in the list row.

## Question: was the machine CPU-saturated between t1 and t2 (resource-usage profile)?

- Command: `profiler-cli counter list --session <resource-usage session>` -> `No counters in this profile.`; `profile info` -> "No significant activity".
- Expected: the machine CPU track as a counter, so `counter info` over a `zoom push` answers it.
- Got: CPU is only in ~10/s `CPU Use` markers (cpuPercent in the payload); the list shows no values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script bucketing `data.cpuPercent` per 2 s.
- What would have answered it: `CPU Use` exposed as a counter, or the marker list/aggregate showing min/avg/max of cpuPercent over the zoomed range.

## Question (review): which process sent the PSessionStore messages the parent received in this window?

- Command: `profiler-cli zoom push 21.5,30.6 --session S` then `profiler-cli thread markers --search PSessionStore --list --limit 0 --session S` (parent main thread).
- Expected: each IPCIn row to name the sender (e.g. "from https://example.com (2/7)" or "WebExtensions").
- Got: IPCIn rows with an empty description; the matching `Runnable` rows name the message but not the sender either.
- Workaround: the same command with `--json` and a script counting `data.sendThreadName` x `data.messageType`.
- What would have answered it: the sender (from `niceDirection`/`sendThreadName`) in the IPC row, or a `--group-by sender` style summary.

## Question (review): was the machine's disk busy while a content process was stuck loading xul.dll (resource-usage profile)?

- Command: `profiler-cli thread markers --search "name:IO" --list --limit 0 --json --session S` after `zoom push 114,132`.
- Expected: the read/write bytes per `IO` marker, in the list or the JSON.
- Got: the list shows `IO` rows with no values, and the list JSON only handle/label/start/duration. `name:IO` also matched `NetIO`.
- Workaround: pass all handles to one `marker info ... --json` call and bucket `read_bytes`/`write_bytes` per second in a script.
- What would have answered it: the same fix as for `CPU Use` above: a counter, or min/avg/max/sum of the payload fields over the zoomed range.

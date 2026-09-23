## Question: in which order did the parent main thread handle these IPC messages, relative to a DOM event and a runnable around them?

- Command: `profiler-cli thread markers --list --limit 0 --search "name:IPCIn,name:ReceiveMessage,name:BrowserTestUtils,name:DOMEvent,name:Runnable" --session S` on a 9 ms zoom.
- Expected: one chronological list where each IPCIn row sits at the time the receiving thread handled it, with its message type (`PBrowser::Msg_OnStateChange`) in the row.
- Got: IPCIn rows placed at the sender's send time, with no message type in the row, and times rounded to the ms. The list could not answer "was the load message handled before the `switchTab` runnable, and the second `OnStateChange` after it".
- Workaround: `--json`, then a script sorting IPCIn by `start + duration` and everything else by `start`, and printing `fields[messageType]`.
- What the default output could have shown: the message type in IPC rows; an option to place IPC markers at their receive/handle time; sub-ms times when the zoom is under ~100 ms.

## Question: when did an IPC message reach the receiving process's IO thread?

- Command: `thread markers --search name:IPCIn --list --json`, reading `data.sendStartTime` / `data.recvEndTime`.
- Expected: those times on the same zero as the displayed `start`.
- Got: every `data.*Time` was 7.251 ms later than the same instant on the displayed timeline (`data.startTime - start` = 7.251 ms on every IPC marker of this profile), with nothing in the output saying so. Read as-is, they put the message's arrival after the DOM event it caused.
- Workaround: subtract `data.startTime - start` from each payload time.
- What the default output could have shown: the IO-thread arrival time (`recvEndTime`) as a converted field in `marker info`, on the displayed timeline.
- Addendum: the offset is constant within one profile, and it differs between profiles: 7.251 ms (DRmNKbTtSz-uywZG9w_Rsw), 0.241 ms (UDrV6N1USIGFjmohwvUSZg), 8.178 ms (UbO4TJEHQPexFHl7qe2GVg), on every IPCIn/IPCOut in the windows I dumped.

## Review (review-browser_highlights_section.js.timeout): the two questions above came up again

- Checking this report meant writing both workarounds again, a `--json` dump plus a script, for the same two questions: "which IPC message is this row" and "when did it reach the parent's IO thread, on the displayed timeline". `thread markers --category IPC --list` shows bare `IPCIn`/`IPCOut` rows, and `data.recvEndTime` still carries the 7.251 ms offset. Both entries above cost a second agent time on the same profiles.

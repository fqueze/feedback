## browser_rules_nested_at_rules.js (2026-09-22)

### Question: "did this request fail, with which status, and did it reuse a connection?"
- Command: `profiler-cli thread network --session <s> --search document-builder`
- Expected: the request's `requestStatus` (here `NS_ERROR_ABORT`), `httpVersion`, `responseStatus`, and whether it opened a new connection or reused one (connectStart present or not).
- Got: `m-61 https://example.com/document-builder.sjs?... ??? cache=Unresolved duration=181.75ms`. The failure status only shows in `marker info`.
- Workaround: a script over `thread markers --category Network --list --json` reading `data.requestStatus`, `data.httpVersion`, `data.connectStart`, `data.requestStart`, `data.responseEnd` to get each request's status, and the idle time on the connection before it. The default output could show the status for non-NS_OK requests, and a "reused connection" / "new connection" column.

### Question: "which IPC messages did this process receive, in order, before X?"
- Command: `profiler-cli thread markers --session <s> --search Msg_ --list` on a content main thread.
- Expected: one row per message with its `messageType` (e.g. `PContent::Msg_AddLoadedOrigin`), send time and handled time.
- Got: `IPCIn  t=6m23s  164.87ms` rows with no message type in the description; the type is only in `marker info` / the JSON `data.messageType`. The Runnable markers carry it for some messages only.
- Workaround: script over `--json` sorting receiving-side IPC markers by `data.sendStartTime` and printing `data.messageSeqno` and `data.messageType`. That is what showed the failing process never got `AddLoadedOrigin` before `RedirectToRealChannel`. Showing `messageType` in the IPC markers' list description would have answered it directly.

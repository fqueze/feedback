# profiler-cli feedback — browser_net_ws-stacks.js

## Question: "which IPC messages did the parent exchange with the new content process (pid N), in order?"

- Command: `profiler-cli thread markers --session browser_net_ws-stacks.js-1 --category IPC --search "1640" --list --limit 0`
- Expected: the IPC markers whose Other Pid is 1640 (or an empty list meaning there are none).
- Got: "No markers match". The same search with a pid that did have traffic (`10068`) also matches nothing, so the empty answer cannot be trusted: `otherPid` holds a number and `--search` does not seem to match numeric payload values.
- Also: `--list` rows for `IPCIn`/`IPCOut` show no message type, direction peer or pid at all, just "IPCOut", so the list cannot answer the question either.
- Workaround: `--list --json` for handles, then `marker info <all handles> --json`, and a Python script over `fields[]` (`messageType`, `otherPid`, `sendThreadName`, `recvThreadName`). Two extra calls and a script per time window.
- What would have answered it: IPC rows in `--list` showing `messageType` and the peer (`→ pid 1640` / `← http://example.com (2/2)`), and `--search otherPid:1640` working.

## Question: "which document loads were cancelled, and with which status?"

- Command: `profiler-cli thread markers --session browser_net_ws-stacks.js-1 --search "NS_ERROR_DOCUMENT_LOAD_LISTENER_NO_PARENT_CHANNEL" --list` (also `requestStatus:…` and `STATUS_CANCEL`)
- Expected: the `Load N: <url>` Network marker whose `requestStatus` is that value.
- Got: "No markers match" for all three, although `marker info` shows `status: STATUS_CANCEL`, `requestStatus: NS_ERROR_DOCUMENT_LOAD_LISTENER_NO_PARENT_CHANNEL` on that marker ("Fields (raw payload, no schema)"). The guide says a bare term matches all payload values; it does not for Network markers.
- Workaround: list the `Load` markers by URL and `marker info` them one by one to compare statuses.
- What would have answered it: search over schema-less payload fields, or `status`/`requestStatus` in the `--list` row of Network `Load` markers.

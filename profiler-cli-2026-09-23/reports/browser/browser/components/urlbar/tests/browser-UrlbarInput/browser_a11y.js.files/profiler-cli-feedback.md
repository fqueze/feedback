## `load` of a bare per-test profile URL selects a WebExtensions thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/GXSrhiYdTx2kI7Xx6hPjXg/runs/0/artifacts/public/test_info/profile_browser_a11y.js.json --session <s>`, then `thread markers --search searchmodechanged --list`
- Expected: the parent process GeckoMain selected, as when the same profile is loaded from a profiler.firefox.com link with `thread=0`.
- Got: `t-15 (GeckoMain, WebExtensions)` selected, so the search returned "No markers match". Only the header line gave it away.
- Workaround: `thread select t-0` after every bare-URL load.

## IPC rows in `thread markers --list` do not show the message type

- Command: `thread markers --category IPC --list --limit 0` (and `--search IDB --list`)
- Question: "which IPC messages were exchanged between the tab switch and the failure?"
- Got: rows with only `IPCOut`/`IPCIn` and a duration. The message type (`PBackgroundIDBDatabase::Msg_PBackgroundIDBTransactionConstructor`) only shows in `marker info`, one marker at a time.
- Could have shown: the `Type` field in the row's description, as DOMEvent rows show the event type.

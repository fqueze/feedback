## Times over a minute lose precision (2026-09-22)

- Command: `profiler-cli marker info m-6 --session browser_resources_console_messages_workers.js-2` (and `thread markers --list`) on a 61 s profile.
- Expected: the marker time to the millisecond, like `t=16.823s` for markers under a minute.
- Got: `Time: 1m1s (instant)` and `t=1m1s`. The exact timeout time cannot be quoted.
- Workaround: none; the report says "t≈61 s".

## `load` selects a non-parent thread by default (2026-09-22)

- Command: `profiler-cli load <per-test mochitest profile URL> --session ...`
- Expected: the parent process GeckoMain (t-0), which holds the test log.
- Got: `Selected thread: t-30 (GeckoMain, WebExtensions)`.
- Workaround: `thread select t-0` after every load.

## Which IPC messages did this thread send during this script? (review, 2026-09-22)

- Command: `profiler-cli thread markers --session review-browser_resources_console_messages_workers.js-1 --list --limit 0` zoomed to one `ScriptExecution` marker on a content GeckoMain.
- Expected: each `IPCOut`/`IPCIn` row to show its message type, e.g. `PBackground::Msg_PSharedWorkerConstructor`, and its phase.
- Got: bare `IPCOut` rows, some with negative durations (`-1,195,329ns`), so every row needed a `marker info`.
- Workaround: `marker info m-a m-b ... | rg 'Type: P|phase'`.

## `thread network --search a,b` matches nothing (review, 2026-09-22)

- Command: `profiler-cli thread network --session ... --search 'test_worker.js,test_service_worker' --limit 0`
- Expected: both sets of requests, since a comma is OR in `thread markers --search`.
- Got: `0 requests (filtered from 24)`, which I first read as "the scripts were never loaded".
- Workaround: one `--search` per term.

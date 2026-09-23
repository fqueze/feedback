## Marker times in `--list` lose sub-second precision on long profiles

- Command: `profiler-cli thread markers --category Test --search browser_visibleTabs_contextMenu --list --limit 12 --session <s>` on a 6m13s profile (task RauZGwFASAKVlrFg3mOaQw)
- Expected: times such as `t=371.869s`, as on short profiles, where the order of events 10 ms apart is the whole question.
- Got: `t=6m12s` on every row, and `marker info` also printed `Time: 6m12s (instant)`, so I could not order the test log against the IPC markers.
- Workaround: `--list --json` and read `start` from `flatMarkers`, then a python one-liner to print them.
- Could have shown: milliseconds on minute-scale times (for example `6m11.869s`), in both `--list` and `marker info`.

## IPC marker rows in `--list` have no description

- Command: `profiler-cli thread markers --search "PBrowser::Msg_OnStateChange,..." --list --limit 0 --session <s>`
- Expected: the message type and sender on each IPCIn row, like `Runnable` rows show their task name.
- Got: bare `IPCIn` rows. I needed `marker info` on each handle, or `--json` fields, to see `PBrowser::Msg_OnStateChange`, `From: Web Content (5/6)` and the send time.
- Workaround: `marker info m-a m-b ...` in batches, then `--json` plus a script.

## Question: when did the one sample under function X happen? (review)

- Command: `profiler-cli thread samples-top-down --include-idle --session <s>` inside `zoom push m-204` (a 730 ms Jank), which showed one sample (1.4%) under `ext-tabs.js!...fireForTab/<` next to 98.6% under `SimpleTest.waitForFocus/<`.
- Expected: a way to get that sample's timestamp, to tell whether it breaks "one callback for the whole jank".
- Got: percentages only. `thread samples --includes-function f-3100` gives counts, not times.
- Workaround: bisected with `zoom push 5.90,5.942` and then `5.925,5.945`, re-reading the tree each time.
- Could have shown: a `--list` / `--times` option on the samples commands that prints the sample times (or first/last time) of a node.

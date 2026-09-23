## Marker times past 1 minute lose all sub-minute precision

- Command: `profiler-cli marker info m-396 m-397 m-47 --session browser-browser_tabKeyNav.js-1` (and `thread markers --list`)
- Expected: a time I can order against neighbours, e.g. `t=60.728s` or `1m0.728s`.
- Got: `Time: 1m1s (instant)` / `t=1m` for markers 4 ms apart, so ordering IPC/JSActor messages after the 60 s mark from the text output was impossible (60.339 and 60.722 both print as `1m`).
- Workaround: `--json` and read `start` in ms.

## Question: "which IPC messages (by type) went in and out in this window?"

- Command: `profiler-cli thread markers --session browser-browser_tabKeyNav.js-1 --search "IDB,remote-settings" --list --limit 0`
- Expected: each IPCIn/IPCOut row to show its `messageType` (e.g. `PBackgroundIDBRequest::Msg___delete__`), as Runnable rows show their task name.
- Got: rows read only `IPCOut` / `IPCIn` with no type, so the search matched on a payload the list does not display; I had to script over `--json` to see which were IndexedDB requests and replies.
- Workaround: `--json` and print `fields[messageType]`.

## Question: "was the machine's disk saturated while this test ran, and which tests overlapped the burst?"

- Command: `profiler-cli thread markers --session browser-browser_tabKeyNav.js-ru3 --search "name:IO,name:test" --list --limit 0 --json` piped through a script that merges consecutive 100 ms `IO` buckets with more than 5 MB written into spans and lists the `test` markers overlapping each.
- Expected: something like `counter info` for the resource-usage IO/CPU markers (they are markers, not counters, so `counter list` says "No counters"), or a summary of IO per test marker.
- Got: 12,070 `IO` markers with human-formatted sizes (`16.8MB`, `4,096B`) that must be parsed back into numbers.
- Workaround: the script above.


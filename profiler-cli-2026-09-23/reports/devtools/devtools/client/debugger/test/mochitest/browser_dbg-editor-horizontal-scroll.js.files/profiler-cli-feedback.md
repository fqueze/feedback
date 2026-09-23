## Question: when did the HTTP/3 connection this request used last receive data?

- Command: `profiler-cli thread network --session <s> --sort start --limit 0` (zoomed on the 90 s before the failure).
- Expected: per request, the HTTP version, `responseEnd`, and whether it opened a new connection or reused one (the marker's `connectStart` identifies the connection: reused requests carry the connection's original `connectStart`).
- Got: URL, duration and phase names only; `httpVersion` and cache status only in `--json`, `responseEnd`/`connectStart` only in `marker info`. Had to script over `--json` and then `marker info` each candidate to find that the last page-connection response was 30.01 s before the failing dispatch.
- Could have shown: a `conn` column (e.g. the `connectStart` or a connection id) and `responseEnd`, so "last use of this connection" is readable directly.

## Question: the order and exact times of markers inside a 340 ms window

- Command: `profiler-cli thread markers --session <s> --search "Network,NotifyObservers,DOMEvent" --list --limit 0` inside `zoom push 502.96,503.3`.
- Expected: timestamps precise enough to order events a few ms apart.
- Got: every row shows `t=8m23s`; had to use `--json` and print `start` myself.
- Workaround: `--json` + python. A timestamp with ms precision (or relative to the zoom start) in the text list would have answered it.

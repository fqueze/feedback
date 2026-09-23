## Question needing a script: "in which order did these markers happen during this 50 ms test?"

- Command: `profiler-cli thread markers --session S --search "RefreshDriverTick,name:DOMEvent,DoFlushPendingNotifications,setTimeout,TEST-" --list --limit 0`
- Expected: a time column that orders markers within one test.
- Got: every row says `t=5m58s` (the whole test is 58 ms, five minutes into the profile), so ticks, events and flushes cannot be spaced from the default output, and whether a tick came before or after a given event has to be taken on trust from the row order.
- Workaround: `--json` and print `start` with 3 decimals per row (script).
- What could show it: millisecond (or sub-ms) times in `--list` once the view is zoomed to under a few seconds, or a `--time-format ms` flag.

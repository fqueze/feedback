## Question: "the millisecond timestamps of these markers in a long profile"

- Command: `profiler-cli zoom push 778,788` then `thread markers --search '...' --list --limit 0` on a 23-minute resource-usage profile.
- Expected: after zooming to 10 s, times precise enough to order markers from different processes (DOMWINDOW destroyed vs "Completed ShutdownLeaks collections").
- Got: the time column shows `t=13m`, `t=13m2s`, whole seconds at best, and `marker info` shows `Time: 13m - 13m6s`.
- Workaround: `marker info m-a m-b ... --json` and a script reading `.markers[].start/.end`.
- Could have shown: times in ms, relative to the zoom start, once the zoom is short (or show the first-row offset in ms).

## Order of log lines within one second, in a 22-minute resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search browser_popup-record-capture-view --list --limit 0`
- Expected: timestamps precise enough to order and time the test's log lines (they are ~1-400 ms apart).
- Got: every row shows `t=14m26s` / `t=14m27s`: second resolution, so a 0.4 s capture and a 0.2 s popup opening are invisible, and the 44 s hang only shows as the jump from 14m27s to 15m14s.
- Workaround: `--json` and a script printing `start/1000` with ms precision.
- What would have answered it: millisecond (or at least 0.1 s) precision in `--list` times when the rows are dense, e.g. `t=14m26.771s`.

## Marker list timestamps rounded to the second on long profiles

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_tab_move.js --list --limit 0 --session browser-tab_move-1` (15-minute per-test profile)
- Question: when, to the millisecond, did each step of the test's log happen, so as to order it against DOMEvent markers?
- Expected: timestamps precise enough to order markers a few ms apart.
- Got: `t=15m15s` for every marker in the same second; `marker info` also prints `Time: 15m15s`.
- Workaround: `--json` and a Python script printing `start/1000`.

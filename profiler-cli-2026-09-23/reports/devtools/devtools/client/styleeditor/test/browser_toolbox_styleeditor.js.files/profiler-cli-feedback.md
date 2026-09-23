# profiler-cli feedback

## Question: at what exact time did this marker happen, so I can zoom right after it?

- Command: `profiler-cli marker info m-15 m-70 m-16 --session browser_toolbox_styleeditor.js-1`
- Expected: the time with millisecond precision (e.g. `297.969s`), usable directly in `zoom push`.
- Got: `Time: 4m58s (instant)` — rounded to the second. `thread markers --list` has the same `t=4m58s` resolution. In an 11-minute profile, a second spans hundreds of markers.
- Workaround: `marker info ... --json | jq '.. | objects | select(has("start")) | .start'` (ms), then divide by 1000 for `zoom push`.

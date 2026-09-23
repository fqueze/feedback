## `thread markers --list` shows times as `t=3m5s` even inside a 22 ms zoom

- Question: in what order, to the millisecond, did popupshowing, the skipped vsync tick, popupshown and the failing assertion happen?
- Command: `profiler-cli zoom push 184.890,184.912 --session ...; profiler-cli thread markers --search '...' --list --limit 0 --session ...`
- Expected: start times precise enough to order markers within the zoom, such as `t=184.8931s`, or ms relative to the zoom start.
- Got: every row showed `t=3m5s`, so I could not order them or measure the gaps between them.
- Workaround: `--json`, then a Python script printing `start`/`end` from `flatMarkers`. I did this for each of the 3 profiles.

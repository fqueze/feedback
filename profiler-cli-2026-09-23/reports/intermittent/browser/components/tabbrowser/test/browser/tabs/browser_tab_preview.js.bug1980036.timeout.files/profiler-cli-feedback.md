## Time bases disagree, and the list's time column is too coarse to order markers

- Commands: `profiler-cli marker info m-130 --json` (→ `"start": 219943.83`, `context.rootRange.start: 7.4943`), `profiler-cli zoom push 219.93,219.975`, `profiler-cli thread markers --list` (→ `t=3m40s` on every row).
- Expected: one documented time base. `rootRange.start = 7.49` suggested JSON `start` is relative to an offset, so I subtracted it; `zoom push` actually takes the raw value (start/1000), so my ranges were 7.5 ms off and a listing seemed to "miss" markers I knew were in range.
- Got: 20 minutes lost reconciling times. Also, zoomed to 45 ms, every `--list` row still shows `t=3m40s`: the order of events 0.1 ms apart (a synthesized event, then a refresh tick, then the mouseover it caused) can only be read through `--json`.
- Workaround: `--json` and a Python script printing `start/1000` with 4 decimals.
- Could have shown: `t=` in ms (or with sub-second precision) when the view is shorter than a few seconds; and say in `zoom push --help` / JSON which zero the times use.

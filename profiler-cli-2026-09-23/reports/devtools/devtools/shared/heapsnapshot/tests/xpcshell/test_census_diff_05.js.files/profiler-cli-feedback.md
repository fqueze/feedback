## Question: "at what exact time (ms) was this marker logged?"

- Command: `profiler-cli marker info m-4 m-20 --session <id>` prints `Time: 3m29s (instant)`. `thread markers --list` also rounds to the second.
- Why it mattered: I had to line the harness's `Launched Test App` markers up with logcat's `Start proc` lines on the device clock, to within about 100 ms. Second-level rounding is too coarse to place an LMK kill 1.6 s after launch.
- Workaround: `marker info ... --json | python3 -c '... r["start"] ...'`, which has ms precision.
- What could show it: `marker info` could print the time in ms (e.g. `209.159 s`), or offer a `--precise` flag.

## Question (review): "is marker=N in this link the marker the report quotes?"

- Command: `profiler-cli marker info m-1 m-3 m-4 --session <id>` (after `thread markers --search test_census_diff_05 --list`).
- Expected: the marker's index (the `marker=` URL value) and its start in ms, in the default output.
- Got: neither; `Time: 3m27s`. Needed `marker info … --json | python3 -c '… r["markerIndex"], r["start"] …'` for each of ~20 links.
- What could show it: print `index N` and the ms start in `marker info`, or accept `marker info --index 11813` so a link's marker can be opened directly.

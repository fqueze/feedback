## Marker times are shown to the second in long profiles

- Command: `profiler-cli marker info m-84 m-85 m-103 --session <s>` and `thread markers --list` on a 22-minute resource-usage profile.
- Question: "how many ms passed between 'exiting test' and the process's late shutdown warnings?" (the answer was whether a 2000 ms shutdown timeout had run out).
- Got: `Time: 12m16s`, `12m19s`. Rounded to the second, so the gap could not be read.
- Workaround: `marker info --json`, reading `start`, one call per marker.
- Could show: milliseconds (e.g. `12m16.228s`) in `marker info` at least, or when the list is zoomed to a few seconds.

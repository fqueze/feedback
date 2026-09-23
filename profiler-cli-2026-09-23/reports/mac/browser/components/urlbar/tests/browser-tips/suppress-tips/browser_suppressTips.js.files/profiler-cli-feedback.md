## Question: was a window destroyed before or after a given log line, in a 20-minute resource-usage profile

- Command: `profiler-cli marker info m-548 m-549 m-779 --session <s>` on a resource-usage profile (20m44s long)
- Expected: start/end times precise enough to order markers a few ms apart.
- Got: `Time: 12m26s - 12m31s (4.163s)` and `Time: 12m31s (instant)`: rounded to the whole second, so the DOMWINDOW end and the `Completed ShutdownLeaks collections in process 3070` line (14 ms apart) look simultaneous. `thread markers --list` has the same resolution (`t=12m26s`).
- Workaround: `marker info ... --json` and read `start`/`end` (746359.015 → 750532.015 vs 750518.015).
- Could show: millisecond precision (e.g. `12m30.532s`) whenever the profile is longer than a minute, at least in `marker info`.

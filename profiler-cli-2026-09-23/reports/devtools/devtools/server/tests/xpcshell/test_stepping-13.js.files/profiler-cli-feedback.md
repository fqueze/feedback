## Question: which tests were in progress at time t (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "name:test" --json` + a Python script keeping `test` interval markers with `start <= t <= start+duration`.
- Expected: a way to list the interval markers overlapping one instant, e.g. `thread markers --at 63.896 --search name:test`. Here that is "which tests were running when the first 0x80073CFC launch failure happened".
- Got: `zoom push t,t+ε` does keep the overlapping intervals, but they are mixed with every other marker, and ordered by start, so the answer is buried under hundreds of 45 s TIMEOUT intervals.
- Workaround: the JSON `flatMarkers` and a script.

## Question: the instant markers logged in a short window, without the long intervals around it

- Command: `profiler-cli zoom push 93.071,93.080` then `thread markers --list --limit 200`.
- Expected: the log lines replayed at that moment, i.e. the markers *starting* in the window.
- Got: 314 markers, the first ~290 of them being `Phase`, `parallel` and per-test `test` intervals that started tens of seconds earlier and merely overlap the window.
- Workaround: `--search "-name:test"` plus `--json` and a script sorting by start. A `--starting-in-range` flag (or `--instant`) would answer it directly.

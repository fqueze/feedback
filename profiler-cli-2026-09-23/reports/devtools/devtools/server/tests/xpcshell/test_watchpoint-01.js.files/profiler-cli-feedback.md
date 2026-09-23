# profiler-cli feedback — test_watchpoint-01.js

## Question: how many ms after the first launch failure did this test start, when both are past t=60 s?

- Command: `profiler-cli thread markers --session test_watchpoint-01.js-2 --search test_watchpoint-01 --list --limit 0` (and `marker info m-1 m-3`)
- Expected: start times at ms precision, as they are below 60 s (`t=53.084s`).
- Got: `t=2m14s` for both the test marker and its `will retry` INFO, and `t=2m12s` for the launch failure; `marker info` prints `Time: 2m14s (instant)` too. Past one minute, the sub-second part is dropped, so the 30 ms between start and `will retry` (the evidence that the test never ran) cannot be read.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`.
- What the output could have shown: `t=2m13.582s`, i.e. keep ms precision whatever the magnitude, at least in `--list` and `marker info`.

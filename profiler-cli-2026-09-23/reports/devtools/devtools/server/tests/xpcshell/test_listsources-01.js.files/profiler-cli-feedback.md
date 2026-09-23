## Question: "at what exact second did these markers happen, past the first minute?"

- Command: `profiler-cli thread markers --session test_listsources-01.js-3 --search "test_listsources-01,2147009284,Detected crash" --list --limit 0`
- Expected: start times with millisecond precision, as below 60 s (`t=47.700s`).
- Got: past 60 s, times are rounded to the second (`t=2m13s`, `t=2m7s`, `t=1m33s`). Ordering a crash, a launch failure and a test start that are 20-300 ms apart around t=131 s was impossible.
- Workaround: `--json` plus a Python one-liner printing `flatMarkers[].start / 1000`. The list output could print `t=131.676s` (or `2m11.676s`) instead.

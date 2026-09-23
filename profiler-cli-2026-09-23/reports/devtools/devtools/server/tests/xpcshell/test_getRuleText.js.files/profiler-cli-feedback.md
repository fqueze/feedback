## Question: the exact time of a marker after the first minute of the profile

- Command: `profiler-cli thread markers --session test_getRuleText.js-2 --search test_getRuleText --list --limit 0` and `profiler-cli marker info m-1 m-3 --session test_getRuleText.js-2`
- Expected: start times with millisecond precision (the TIMEOUT marker and the "will retry" INFO are 39 ms apart, which is the whole point).
- Got: `t=2m13s` for both, in the list and in `marker info` (`Time: 2m13s - 2m58s`). Under one minute the list shows `t=46.538s`, so precision drops from 1 ms to 1 s past 60 s.
- Workaround: `marker info m-3 --json` and read `start` (133227.908). The default output could keep milliseconds, e.g. `2m13.228s`.

## Question: how many markers matching X *start* inside a time window, and which is the last one

- Command: `profiler-cli thread markers --session test_getRuleText.js-1 --search "status:PASS" --list --limit 0` (116 rows, read by eye) and `--search status:TIMEOUT --list --limit 0 --json | python3 ...` to count the 1057 TIMEOUTs starting between 45.9 and 52 s.
- Expected: a way to ask "markers of this search starting in [a,b]" or a start-time histogram in the aggregated view.
- Got: `zoom push a,b` keeps markers overlapping the range (a 45 s TIMEOUT that started before it still shows), and the aggregated view gives only min/avg/max duration.
- Workaround: `--json` and a script over `flatMarkers[].start`.

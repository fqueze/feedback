# profiler-cli feedback (test_pauselifetime-01.js)

## Question: how long after the test started did the harness log "will retry"

- Command: `profiler-cli thread markers --session test_pauselifetime-01.js-1 --search test_pauselifetime-01 --list --limit 0`
- Expected: times precise enough to compare two markers tens of ms apart.
- Got: past one minute, times print rounded to the second (`t=2m13s` for both the `test` marker at 133.376 s and the `will retry` INFO at 133.416 s), so the 40 ms gap, which is the whole diagnosis (a launch that failed at once), cannot be seen.
- Workaround: `profiler-cli marker info m-1 m-3 --json` and reading `start` in ms.
- Could have shown: ms precision (e.g. `t=2m13.376s`) in `--list`, or a relative column.

## Question: which tests were running at time T, and which tests failed to launch within N ms of starting

- Command: `profiler-cli thread markers --session test_pauselifetime-01.js-1 --category Tasks --list --limit 0 --json`, then a Python script over `flatMarkers` joining `test` markers with `will retry` INFO markers by test name.
- Expected: a list of interval markers overlapping a point in time, with their start/end in ms.
- Got: `zoom push T,T+0.001` then `--list` does list overlapping markers, but with second-rounded times and without the end time, so I could not sort by start or see which ended before T.
- Could have shown: start and end with ms precision in `--list`.

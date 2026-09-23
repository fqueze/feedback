## Marker start times past 60 s lose their milliseconds in `thread markers --list`

- Question: at what exact time did this test start, compared with the first child-launch failure 1-2 s before it?
- Command: `profiler-cli thread markers --session test_new_source-01.js-2 --search test_new_source-01.js --list --limit 0` (and `marker info m-1`)
- Expected: `t=133.258s` (or `2m13.258s`).
- Got: `t=2m13s` in the list and `Time: 2m13s - 2m58s (45.043s)` in `marker info`; the durations keep ms, the start times do not.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`. The list could print `2m13.258s`.

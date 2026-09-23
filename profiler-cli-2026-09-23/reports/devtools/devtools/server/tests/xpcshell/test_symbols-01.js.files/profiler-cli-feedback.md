## Marker times past 60 s lose their milliseconds in `--list`
- Command: `profiler-cli thread markers --session test_symbols-01.js-2 --search test_symbols-01 --list --limit 0`
- Expected: start times with ms precision throughout (e.g. `t=133.549s` or `t=2m13.549s`).
- Got: `t=2m14s` for the TIMEOUT marker and `t=2m14s` for the `will retry` INFO 40 ms later; under 60 s the same list prints `t=46.936s`. The 40 ms gap between the two, which is the finding, is invisible.
- Workaround: `profiler-cli marker info m-1 m-3 --json` and read `start`.

## Question: how many markers of a search started after time T, and over what start range
- Command: `profiler-cli thread markers --session test_symbols-01.js-1 --search "status:TIMEOUT"` (aggregate), then `--list --limit 0 --json` plus a Python script.
- The aggregate gives count and duration min/avg/max, but not the first and last start time of the group, nor a split before/after a timestamp. A "starts: first … last" line per group would have answered "did 1057 of the 1060 TIMEOUTs start within 4 s after the first launch failure" without a script.

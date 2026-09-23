## Marker times above 60 s lose their milliseconds in text output

- Command: `profiler-cli thread markers --session test_require_lazy.js-3 --search test_require_lazy --list --limit 0` and `profiler-cli marker info m-1 m-3 --session ...`
- Question: how many ms after the test's start did its "will retry" line come (launch failure vs. real run)?
- Expected: `t=65.886s` / `t=65.897s`, as for times under a minute (`t=47.040s`).
- Got: `t=1m6s` for both, so the 11 ms gap is invisible; `marker info` also prints `Time: 1m6s (instant)`.
- Workaround: `--list --json` and read `flatMarkers[].start`. The default output could keep ms precision (`1m5.886s`).

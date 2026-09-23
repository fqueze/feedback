## Marker times past one minute lose sub-second precision

- Command: `profiler-cli thread markers --search test_objectgrips-nested-proxy --list --limit 0 --session test_objectgrips-nested-proxy.js-2` and `profiler-cli marker info m-1 m-3 m-7 --session ...`
- Question: how long after the first launch failure (0x80073CFC) did this test start? Both markers are at t≈2m12-2m13s.
- Expected: millisecond times, as shown below one minute (`t=52.792s`).
- Got: `t=2m12s` and `t=2m13s`, in both the list and `marker info` (`Time: 2m13s - 2m58s (45.028s)`). The 1.7 s gap cannot be read from them.
- Workaround: `marker info ... --json` and read `start`.

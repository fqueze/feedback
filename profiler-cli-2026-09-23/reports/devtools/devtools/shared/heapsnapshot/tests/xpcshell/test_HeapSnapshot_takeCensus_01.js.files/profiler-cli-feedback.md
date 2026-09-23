## Times past one minute lose sub-second precision

- Command: `profiler-cli thread markers --session <s> --search early_shutdown --list` and `profiler-cli marker info m-23 m-1 --session <s>` on the resource-usage profile of task ZtblArCLSx6O6KzfEkL1YQ.
- Question: did the first `Failed to launch ... subprocess` warning come before or after `test_ext_background_early_shutdown.js` ended?
- Expected: millisecond times, as below one minute (`t=50.350s`).
- Got: `Time: 47.432s - 1m4s (16.439s)` and `Time: 1m4s (instant)`: both events print as `1m4s`, so their order is unreadable.
- Workaround: `marker info m-1 --json` for `start` (63895.915), and adding start + duration by hand for the other one.

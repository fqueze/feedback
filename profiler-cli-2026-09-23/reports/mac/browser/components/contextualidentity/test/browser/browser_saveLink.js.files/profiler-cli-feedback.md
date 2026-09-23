## Times past 60 s lose their milliseconds

- Command: `profiler-cli thread markers --category Test --search browser_saveLink --list --limit 0 --session browser_saveLink.js-4` and `profiler-cli marker info m-7 m-16 --session browser_saveLink.js-4` (Linux per-test profile of task fuyireXiTPqXWTPbF2mdrA, the test ran at ~60.4 s).
- Expected: `t=60.365s`, or `t=1m0.365s`, as below 60 s (`t=24.812s`).
- Got: `t=1m` and `t=1m1s` in the list, `Time: 1m (instant)` in `marker info`. A 20 ms race cannot be read from that; every marker in the window prints the same `t=1m`.
- Workaround: `--json` and read `start` (ms).

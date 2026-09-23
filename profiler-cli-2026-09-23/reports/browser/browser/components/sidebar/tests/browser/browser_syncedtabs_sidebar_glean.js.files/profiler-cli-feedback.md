## Question: how far apart are two markers, when the profile is over a minute long?
- Command: `profiler-cli thread markers --category Test --search browser_syncedtabs_sidebar --list --limit 0` and `profiler-cli marker info m-20`
- Expected: timestamps precise enough to order and space events (ms).
- Got: `t=1m50s` for both the timeout and the next task's failure, and `Time: 1m50s` in `marker info`; past one minute the times are rounded to the second.
- Workaround: `marker info m-6 m-17 ... --json` and read `start`/`end` in ms.
- What the output could show: `t=109.943s` (or `1m49.943s`) in the list and in `marker info`.

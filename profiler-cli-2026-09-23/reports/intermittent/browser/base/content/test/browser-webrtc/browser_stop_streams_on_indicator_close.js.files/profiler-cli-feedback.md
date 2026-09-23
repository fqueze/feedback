## Ordering markers that fall in the same second

- Command: `profiler-cli thread markers --list ...` and `profiler-cli marker info m-205 m-203 m-276 ...`
- Question: which came first — the display menuitem command, the Allow click, `getUserMedia:privileged:allow`, the first lock-wait sample — all within 50 ms.
- Expected: millisecond start times in the text output (at least in `marker info`, and in `--list` when a zoom is narrower than a few seconds).
- Got: every row reads `t=1m34s`; had to pipe `marker info --json` through python to get `start`.

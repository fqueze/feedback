## `marker info` text output rounds times to whole seconds on long profiles

- Command: `profiler-cli marker info m-1 m-3 --session <s>` on a 23-minute resource-usage profile
- Expected: millisecond times, as the slot-by-elimination reasoning needs ordering within ~100 ms.
- Got: `Time: 13m22s - 13m24s (2.341s)` and `Time: 13m24s (instant)`. `thread markers --list` also prints `t=13m24s`.
- Workaround: `--json` and read `.start` (ms).

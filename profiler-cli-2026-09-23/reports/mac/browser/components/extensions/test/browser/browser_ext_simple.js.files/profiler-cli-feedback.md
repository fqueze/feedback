## Question: when did each line of this test's log happen, to the millisecond?

- Command: `profiler-cli thread markers --session browser_ext_simple.js-1 --category Test --search browser_ext_simple --list --limit 0`
- Expected: a time column precise enough to order and cite the markers (the test's 23 markers span 240.908-249.015 s).
- Got: `t=4m1s` for the first 19 markers and `t=4m9s` for the rest; the column rounds to the second once the profile is past one minute, so the log cannot be read or cited from the default output.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 3 decimals. Needed the same for every `--list` in this report (vsync waits, uuid pref writes, popup DocumentLoad).
- What the output could have shown: `t=240.908s` (ms precision), as `marker info` already does.

## `thread markers --list` times lose precision past one minute

- Command: `profiler-cli thread markers --category Test --search browser_resources_scope_flag --list --limit 0 --session browser_resources_scope_flag.js-1`
- Question: in which order, to the millisecond, did the pref write, the pref reads and the console error happen at the start of the test (all within 4 ms, at t=60.72 s)?
- Expected: times like `t=60.7261s`, as the list prints under a minute (`t=2.885s`).
- Got: every row past 60 s reads `t=1m1s` or `t=1m2s`, so 25 markers spread over 2 s all show the same two times, and zooming in does not change it.
- Workaround: `--json` and a Python one-liner printing `start/1000` rounded to 0.1 ms.

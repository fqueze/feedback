## `thread markers --list` rounds times to whole seconds past 1 minute

- Command: `profiler-cli thread markers --session test_stepping-02.js-2 --search test_stepping-02 --list --limit 0`
- Question: how long after the `test` marker's start did the harness log `failed or timed out, will retry` (did the test ever launch)?
- Expected: start times with ms precision, e.g. `t=65.708s` and `t=65.748s`.
- Got: both rows show `t=1m6s`, so the 40 ms gap is invisible.
- Workaround: `profiler-cli marker info m-1 m-3 --json` and read `start`. Keeping ms precision in the `t=` column (e.g. `t=1m5.708s`) would have answered it directly.

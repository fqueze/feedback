## Times past 60 s lose their milliseconds

- Command: `profiler-cli marker info m-6 m-8 --session test_breakpoint-18.js-3` (and `thread markers --list`) on ZtblArCLSx6O6KzfEkL1YQ's resource-usage profile.
- Question: how many ms after the test's start did the harness log "will retry"? (It tells a launch that raised from a test that ran.)
- Expected: `t=65.123s` for both markers, as it prints `t=51.648s` below 60 s.
- Got: `Time: 1m5s - 1m50s (45.030s)` and `Time: 1m5s (instant)`: both round to the same second, so the gap is unreadable.
- Workaround: none taken; used the 4 jobs where the break fell before 60 s.

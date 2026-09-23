## Marker times in a long profile lose sub-second precision

- Command: `profiler-cli thread markers --session 2052757-1 --search ReplaceWord --list --limit 0` and `profiler-cli marker info m-1 m-8 m-9 m-10 --session 2052757-1` on a 22-minute resource-usage profile (MLoQoUHzTz2n0zEnYRQHRQ).
- Expected: timestamps precise enough to order markers within one test (ms), for example `t=564.802s`.
- Got: every marker in the 7 s test shows `t=9m24s`, and `marker info` shows `Time: 9m24s (instant)`. Markers less than a second apart cannot be told apart or timed against the log.
- Workaround: read the timestamps from the job's live_backing.log instead.

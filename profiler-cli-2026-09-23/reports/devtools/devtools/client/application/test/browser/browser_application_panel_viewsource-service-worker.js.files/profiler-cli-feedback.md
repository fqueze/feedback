## `marker info` rounds times past one minute to whole seconds

- Command: `profiler-cli marker info m-1866 --session vsw-1` (profile of task cDVq3IKiTkO-41JVjO7tBg, marker at 71.003 s)
- Expected: a start time with millisecond precision, as `thread markers --list` prints for markers under a minute (`t=40.443s`).
- Got: `Time: 1m11s - 1m11s (200.58μs)`. `thread markers --list` also prints `t=1m11s` for every marker past 60 s, so markers 30 ms apart can't be told apart or put in order.
- Workaround: `marker info <handles> --json` and read `start`.

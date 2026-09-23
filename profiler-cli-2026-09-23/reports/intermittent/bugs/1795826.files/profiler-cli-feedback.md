## Precise times of markers after the first minute (bug 1795826)
- Question: "how long had the last `js.run` been running when the error Event was logged?" (the call never ended, so it has no marker of its own; needed the end of the previous `wait.byTime` and the Event's time to the ms).
- Command: `profiler-cli thread markers --session 1795826-1 --search name:Event --list --limit 0`
- Got: `t=5m58s` — times after one minute rounded to the second; durations were precise, start times were not.
- Workaround: `--json` and a script reading `start`.
- What could show it: millisecond start times in `--list` (e.g. `t=357.522s`), at least on request.

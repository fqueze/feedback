## Question: the sub-millisecond order and gaps of a handful of markers (a race decided at 1 ms)

- Command: `profiler-cli thread markers --search "popuppositioned,popupshown,tryOnce,..." --list --limit 0 --session <s>` (with a `zoom push` of 8 ms)
- Expected: marker start times precise enough to compute a 0.8 ms gap (the failure is a click 0.877 ms after a `popuppositioned` against a 1 ms delay).
- Got: `t=3.091s` / `t=3.092s` — times rounded to the millisecond even inside an 8 ms zoom, so the gaps that decide the race are invisible; durations are printed in μs but start times are not.
- Workaround: a loop of `profiler-cli marker info <m> --json | python3 -c '... d["start"], d["end"]'` per marker.
- What would have answered it: print start times with a precision adapted to the zoom width (e.g. `t=3091.472ms` when the view is under ~100 ms), or a `--precise-times` flag on `--list`.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked; cost one retry. Noting it only because every agent in the fleet hits it once; `profile-link.py` honours the same variable.

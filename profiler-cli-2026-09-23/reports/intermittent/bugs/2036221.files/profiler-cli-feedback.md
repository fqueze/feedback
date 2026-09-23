## Question: in which order, and how many ms apart, did these log markers happen? (bug 2036221)

- Command: `profiler-cli thread markers --session 2036221-1 --search telemetry-start-worker --list --limit 0` on a 21-minute resource-usage profile.
- Expected: per-marker times precise enough to order events a few ms apart.
- Got: every row is `t=1m15s`. About 30 markers fell in one 600 ms window, and their spacing (for example, the unregister 14 ms after the click) was the whole diagnosis.
- Workaround: `--json`, then a Python script that sorts `flatMarkers[].start` and prints it in ms.
- What would have answered it: sub-second times in `--list` (such as `t=75.148s`) when rows share the same rounded second, or always for `--list`.

## `marker stack` on a C++ warning marker marked ✓ returns only `unknown!null`

- Command: `profiler-cli marker stack m-62 --session 2036221-1` (resource-usage profile, a `C++ warning` marker listed with ✓ = has stack).
- Expected: a stack, or no ✓.
- Got: `[1] unknown!null`. The same happened for m-26 and m-63.
- Workaround: none needed. The source file:line was in the label.

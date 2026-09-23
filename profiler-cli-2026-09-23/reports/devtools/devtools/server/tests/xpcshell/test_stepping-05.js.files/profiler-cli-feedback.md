## `marker info` rounds times past one minute to whole seconds

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_stepping-05.js-2`
- Expected: start times with millisecond precision, e.g. `2m13.489s`, to compare a test's start with a failure 35 ms later.
- Got: `Time: 2m13s - 2m59s (45.033s)` and `Time: 2m14s (instant)`. The list view (`thread markers --list`) rounds the same way. Two markers 35 ms apart cannot be ordered from the default output.
- Workaround: `marker info ... --json`, read `start`.
- Question it could not answer: "how long after this test started did it end" — the default output could have printed `t=2m13.489s`.

## marker info gives times only to the second on long profiles

- Question: exactly when did one marker happen compared with another, to the millisecond? For example the harness's "Failed to get assertion count" warning against the test's start.
- Command: `profiler-cli marker info m-14 --session 2054343-1`
- Got: `Time: 4m46s (instant)`. On a 24-minute resource-usage profile, every marker near the failure shows the same second.
- Workaround: `marker info ... --json` and a script to print `start`.
- What would have answered it: millisecond precision (for example `4m45.957s`) in the text output of `marker info` and `thread markers --list`.

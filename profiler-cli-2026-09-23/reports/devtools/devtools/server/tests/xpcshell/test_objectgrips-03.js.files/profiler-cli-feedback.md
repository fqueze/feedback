## Question: "when exactly did this marker start" past the first minute (marker info / markers --list)

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_objectgrips-03.js-2`
- Expected: millisecond start times, to say that the test started 1.6 s after the first launch failure and failed 37 ms after starting.
- Got: `Time: 2m13s - 2m58s (45.039s)`, `Time: 2m13s (instant)`, `Time: 2m12s (instant)`: rounded to whole seconds once past 1 minute (the `--list` view does the same, while `t=47.751s` is shown with ms under 1 minute).
- Workaround: `--json` and read `start`. Keeping ms precision (e.g. `2m13.279s`) in the text output would answer it.

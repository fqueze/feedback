## Question: how long after the test started did this marker happen? (test_objectgrips-19.js)

- Command: `profiler-cli marker info m-88 m-90 --session <s>`
- Expected: timestamps precise enough to compare two markers of the same second.
- Got: `Time: 2m13s - 2m58s (45.049s)` and `Time: 2m13s (instant)`: the start is rounded to the second, hiding that "will retry" was logged 44 ms after test_start (the fact that shows the test process never ran).
- Workaround: `--json` and read `start`. Could show: ms precision on start times in `marker info` (and in `--list` rows), at least when the profile is under an hour long.

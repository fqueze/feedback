## Marker times lose sub-second precision in long profiles

- Command: `profiler-cli thread markers --session mSPZ-2 --search marSuccessPartialZucchini --list --limit 0` on a 29-minute resource-usage profile.
- Expected: start times precise enough to line up this test's updater launch (t=1079.065 s) with other markers a few hundred ms apart.
- Got: `t=17m56s`, `t=17m59s`, `t=18m1s` — whole seconds only once the profile is past a minute.
- Workaround: `profiler-cli marker info m-200 m-204 ... --json` and read `start`.

## Question: which tests were running during a given time range

- Question: "which other `test` markers overlap the 1.6 s this test's updater ran", to see which tests launched the same callback bundle at the same time.
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python filter on `start`/`duration` against the range.
- What the output could show: with `zoom push 1079.065,1080.645`, a flag to list the interval markers that overlap the zoom (rather than only those starting inside it), with their start and end.

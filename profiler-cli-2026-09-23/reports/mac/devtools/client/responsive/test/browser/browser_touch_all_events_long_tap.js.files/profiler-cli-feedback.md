## `thread markers --list` rounds marker times to whole seconds once the profile is over a minute long

- Question: how long did each step of the test take (e.g. "Run test: ... remoteIFrame" to the TEST-UNEXPECTED-FAIL)?
- Command: `profiler-cli thread markers --category Test --search browser_touch_all_events_long_tap --list --limit 0`
- Expected: millisecond times, as the steps are ~1 s apart (e.g. t=80.200s).
- Got: `t=1m19s`, `t=1m20s`, so steps a second apart are indistinguishable; `marker info` also prints `Time: 1m20s`.
- Workaround: `--json` and read `start`.

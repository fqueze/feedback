## Question: how long did each step of the test take?

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_manage_tabs_tool --list --limit 0 --session ...` on a 41-minute per-test profile.
- Expected: timestamps precise enough to tell how long each `add_task` and each "Opening new AI Window" step took (they are 2-9 s each, several steps per second).
- Got: `t=39m49s`, `t=39m52s` ... rounded to the second, so consecutive markers share a timestamp and durations cannot be read.
- Workaround: `--json` and a Python script over `flatMarkers[].start`. A per-row time with ms precision (or relative to the first listed marker / zoom start) would have answered it directly.

## `profile info` thread CPU ignores the zoom

- Command: `profiler-cli zoom push 2389.05,2493.15` then `profiler-cli profile info`.
- Expected: per-thread CPU within the zoomed range (the header says `View: ts-yd→ts-z9 (1m44s)`).
- Got: the same whole-profile totals as before zooming (`t-0: GeckoMain - 344403.811ms`), while the counters under it do show range sample counts. Easy to misread as range CPU.
- Workaround: `thread samples --include-idle` per thread, reading the Idle category.

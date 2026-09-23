## Marker times in `--list` are rounded to the second in long profiles

- Command: `profiler-cli thread markers --category Test --search browser_ext_webrtc.js --list --limit 0 --session browser_ext_webrtc.js-1` (8m9s profile)
- Expected: each marker's start with ms precision (e.g. `t=479.893s`), as in shorter profiles.
- Got: every row shows `t=8m` or `t=8m1s` or `t=8m9s`; the whole test body (0.8 s) and the start of the vsync wait are indistinguishable.
- Workaround: `--json` and a script reading `flatMarkers[].start`.

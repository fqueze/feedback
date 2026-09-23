## Marker times past one minute lose sub-second precision in list output

- Command: `profiler-cli thread markers --session test_animation_name.js-6 --search "Failed to launch" --list`
- Expected: `t=131.676s` (or `2m11.676s`), as markers under 60 s get (`t=49.973s`).
- Got: `t=2m12s` for every marker in that second (and rounded, not truncated: the marker is at 131.676 s), so ordering and gaps between markers a few ms apart (first launch failure vs the end of another test's interval) cannot be read.
- Workaround: `profiler-cli marker info <m> --json` and read `start` in ms.

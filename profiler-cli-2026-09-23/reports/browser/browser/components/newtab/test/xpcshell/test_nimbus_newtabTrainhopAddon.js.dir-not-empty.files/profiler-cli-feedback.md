## Question: at what millisecond did each of these log markers happen, in a 12-minute resource-usage profile?

- Command: `profiler-cli zoom push 163.55,163.9 --session browser-dne-1`, then
  `profiler-cli thread markers --search test_nimbus_newtabTrainhopAddon.js --list --limit 0` and
  `profiler-cli marker info m-92 m-93 m-94 m-95 m-96 m-97 m-44 m-45`.
- Expected: start times precise enough to order markers that are 1–5 ms apart, at least once
  the view is zoomed to 350 ms.
- Got: every row, and every `marker info` record, reads `t=2m44s` / `Time: 2m44s (instant)`.
  The display precision follows the full profile's length, not the zoomed view's.
- Workaround: `--json` and a Python one-liner printing `flatMarkers[].start / 1000`.
- What would have answered it: millisecond (or sub-ms) start times in `--list` and
  `marker info` whenever the view is shorter than a few seconds, or a `--precise-times` flag.

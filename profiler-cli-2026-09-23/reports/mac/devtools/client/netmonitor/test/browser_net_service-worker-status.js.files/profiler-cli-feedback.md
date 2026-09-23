## Question: in which order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_net_service-worker-status.js --list --limit 0`
  (and the same with `--search NotifyObservers` after `zoom push 111.83,112.02`)
- Expected: a start time precise enough to order markers and measure gaps of a fraction of a
  millisecond, e.g. `t=111.841749s`, at least once the view is zoomed to a 190 ms range.
- Got: every row printed `t=1m52s`, both in the full view and zoomed; `marker info` likewise
  printed `Time: 1m52s - 1m52s (600.75μs)`. The ordering and the 0.93 ms gap between three
  `service-worker-synthesized-response` notifications, which the diagnosis rests on, were not
  readable from the default output.
- Workaround: `--list --json` piped through a Python one-liner printing `start` with 3 decimals.
- What the output could show: the start time with a precision adapted to the view's
  duration (ms or µs digits when the zoom is below a few seconds, or always for a `--list`).

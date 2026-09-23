## Question: "in what order did these markers happen?" (after the first minute of a profile)

- Command: `profiler-cli thread markers --session S --list --limit 0` (on a DOM Worker, 190-197 s into the profile)
- Got: every row printed as `t=3m11s`. At that resolution, the order of a worker's message, its fetch response, a ReleaseRefControlRunnable and the microtasks (all within 1 ms) cannot be read.
- Could show: millisecond times (e.g. `191.1423s`), as it does for times under a minute.
- Workaround: `--json` and a script that prints `start/1000` with 4 decimals.

## Network marker payload times are offset from the marker's own start

- Command: `profiler-cli marker info m-3789` (Load 1030, test-sourcemap.min.js.map)
- Expected: payload `startTime`/`responseStart` on the same timeline as the marker's `Time:`.
- Got: the list shows the marker at 191.1315 s lasting 10.7 ms, but the raw payload says `startTime: 191141.67`, `responseStart: 191143.19` (10 ms later). Read naively, this puts the response after the worker had already received it.
- Workaround: trusted the marker's start and duration, not the payload fields.

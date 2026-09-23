# profiler-cli feedback (browser_aiwindow_firstrun.js)

## Question: "in what order did these markers happen, and how far apart?"
- Command: `profiler-cli thread markers --category Test --search browser_aiwindow_firstrun --list --limit 0 --session ...`, and `marker info m-91 m-92 m-16`
- Expected: start times precise enough to order events that are a few ms apart (e.g. `t=288.5438s`, or ms relative to profile start).
- Got: `t=4m49s` in `--list`, and `Time: 4m46s - 4m46s (4.066ms)` in `marker info`. Every event of interest (a timer callback, a click, a pushState, the failure) fell in the same second. `marker stack` prints `Captured at: 4m46s` too.
- Workaround: `--list --json` and a python script printing `start` to 0.1 ms. That was needed for every timeline in this report.

## Question: "the markers named exactly X (and a few other names) in a range"
- Command: `profiler-cli thread markers --search "setTimeout callback,DOMEvent,ContentTaskUtils,LongIdleTask,CC,GCMajor" --list --limit 0`, then the same with `name:` on every term
- Expected: markers whose name is one of these.
- Got: `CC` matched as a substring of payloads such as `TimingDistribution::accumulate javascript.ion.compile_time` (the "cc" in "accumulate"), which flooded the output (143 KB). Adding `name:` did not narrow it either, because `name` is also a payload key.
- Workaround: `--json` plus a python filter on exact `name`. Being able to match marker names exactly (e.g. `name=CC`) would have answered it.

## Question: "when, and by what, was this setTimeout callback scheduled?" (review-browser_aiwindow_firstrun.js)
- Command: `profiler-cli marker stack m-186 --session ...` on a `setTimeout callback` marker
- Expected: the scheduling time next to the stack, precise enough to match it to the click that scheduled it (here 287314.9 ms, 6.5 ms after the click at 287308.4).
- Got: `Captured at: 4m47s`, and nothing saying that this is the scheduling time rather than the callback time (the callback ran at 4m48s). The precise value only exists as `stack.capturedAt` in `--json`.
- Workaround: `marker stack --json` and a python one-liner reading `stack.capturedAt`, for each timer. Printing `capturedAt` in ms (and labelling it "scheduled at" for timer markers) would have answered it directly.

## Question: at which millisecond did these markers happen, and how far apart are they?

- Command: `profiler-cli thread markers --search name:CompositeToTarget,name:SkippedComposite --list --limit 0 --session <s>` (also `marker info m-N`)
- Expected: marker start times precise enough to line up events a few ms apart (e.g. `t=96.688s` or `96688.11ms`).
- Got: past one minute every row prints `t=1m37s` / `t=1m38s`; `marker info` prints `Time: 1m37s (instant)`. Hundreds of markers share one label, so ordering and gaps within a frame are invisible.
- Workaround: `--json` and a Python script printing `start` and `duration` in ms, plus the gap to the previous row. Needed it for every thread in this investigation (Compositor, Renderer, GPU main, content main).
- What would have answered it: millisecond (or 0.01 ms) resolution in `--list` and `marker info` times, as the `zoom push` range input already accepts; an optional delta-to-previous column would also help.

## Question: what happened on these four threads, interleaved, in this 150 ms window?

- Command: four `thread select` + `thread markers --list --json` calls, merged and sorted by a script (`timeline.py` in this directory).
- Expected: one chronological list across threads (`profile markers --search` sweeps threads, but only for one search term and with the same coarse times).
- Got: per-thread lists only; `profile markers` needs one search and prints `t=1m37s`.
- What would have answered it: `profile markers --thread t-46,t-47,t-48 --search a,b,c --list` with precise times, sorted by start.

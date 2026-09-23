## `zoom push` silently misparses mm:ss input

- Command: `profiler-cli zoom push 21:15,21:36 --session <s>`
- Expected: either a zoom to 21m15s–21m36s (the format `thread markers` prints as `t=21m15s`), or an error.
- Got: `Pushed view range: ts-1 (21s) to ts-1 (21s) (duration: 0s)` — a zero-length zoom at 21 s, no error.
- Workaround: convert to seconds by hand (`zoom push 1275,1296`). Accepting the `21m15s` form the tool itself prints, or rejecting what it cannot parse, would avoid it.

## Question: "how busy was the machine during this window?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` inside a `zoom push`, then a Python script averaging `data.cpuPercent` per second / per minute.
- The default output lists individual CPU Use markers (one every ~130 ms) without their values, so it cannot answer the question without `marker info` on each. `counter list` says "No counters in this profile".
- What would have answered it: a summary of the CPU Use markers' `cpuPercent` over the current view (mean / max, or a bucketed timeline like `counter info`'s "over time"), e.g. from `thread markers --search "CPU Use"` in aggregate mode.

## Question: "what were all the crashed children in this job doing?" (resource-usage profile, 106 CRASH markers)

- Commands: `thread markers --search "child process hang at shutdown" --list --limit 0 --json` to get the handles, then `marker info <106 handles> --json`, then a Python script that classifies each marker's `stack.frames` by key frame names (`_HIMagnifiedMode`, `_LSBundleFindWithNode`, `RenderThread::ShutDown`, ...).
- `marker info m-79..m-184` only covered the first few, because the CRASH markers' handles are not contiguous; I had to pass all 106 handles explicitly.
- The CRASH marker stacks are cut at 20 frames, so 19 of 106 stop in the event loop before the frame that says why.
- What would have answered it: grouping markers by stack, e.g. `thread markers --search X --group-by stack` (or by the N innermost non-breakpad frames), giving counts per distinct stack prefix.

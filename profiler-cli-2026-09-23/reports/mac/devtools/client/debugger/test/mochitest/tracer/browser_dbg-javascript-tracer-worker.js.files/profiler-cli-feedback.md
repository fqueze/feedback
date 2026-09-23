## Default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session <id>`
- Expected: session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: prefix every `profiler-cli` and `profile-link.py` call with `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`. The brief does not mention this; cost one round-trip. (Arguably an environment/brief issue rather than a profiler-cli defect; the error message itself was clear.)


## Question (review): "which marker does a link's `marker=N` point at?"

- Command: `profiler-cli marker info m-6` (and `--list` rows) show the handle, name and time, but not the marker index a profiler link's `marker=N` uses.
- Got the answer only through `profiler-cli marker info m-6 --json | python3 -c '...["markerIndex"]'`, once per marker, for about 20 links.
- The default `marker info` output (or a `--list` column) could print the index, or `marker info` could accept `index:N` directly.

## Question (review): "was the main thread busy or idle between t1 and t2?"

- Command: `profiler-cli zoom push 25.165,25.231` then `profiler-cli thread samples --include-idle`: 1 sample in 66 ms, so no answer from samples.
- Workaround: merged `Runnable` intervals from `thread markers --search name:Runnable --list --limit 0 --json` in a script. It reported 8 ms "gaps" that were in fact covered by a 10 ms `Perform microtasks` marker outside any `Runnable`, so the answer was wrong until I listed every marker in the gap.
- A per-range busy/idle figure (from all top-level interval markers, or the event-loop wait) would answer it; so would saying why a zoomed range has so few samples.

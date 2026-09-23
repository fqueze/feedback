## Question: which m-handle is the link's `marker=N`? (review of this report)

- Command: `profiler-cli load '<link with marker=N>'`, then `profiler-cli marker info m-… ` for candidate handles.
- Expected: `load` names the handle for the link's `marker=N` (or `marker info` default output shows the marker index).
- Got: `load` prints only the thread/range; default `marker info` output has no index, so each check needed `marker info m-… --json | python3 -c '…markerIndex…'`.
- Workaround: find candidates with `thread markers --search`, then script over `--json` per handle.

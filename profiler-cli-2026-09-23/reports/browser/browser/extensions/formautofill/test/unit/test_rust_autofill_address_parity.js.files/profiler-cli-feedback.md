## When was a function sampled?

- Question: "at what time(s) was `updateSavedFieldNames` on the stack" — to place a sample relative to markers (here: before or after a pref write at t=3.643s).
- Command: `profiler-cli thread functions --search updateSavedFieldNames` / `thread samples-top-down --search updateSavedFieldNames` give counts and the tree, but no sample times.
- Workaround: bisected by hand with `zoom push <a>,<b>` + `thread functions --search ... | grep -c f-764`, three ranges.
- What would have answered it: a `--list` (or `--times`) on samples commands with `--search`, printing each matching sample's time, like `thread markers --list` does for markers.

## `load` selects a non-parent thread

- Command: `profiler-cli load <xpcshell per-test profile URL> --session ...`
- Got: `Selected thread: t-4 (GeckoMain, WebExtensions)` (and t-3 on another profile), although t-0 is the parent GeckoMain and the busiest thread.
- Cost: a wasted `thread select` each time; harmless once known, but a query before it silently reads the wrong process.

## `thread samples` after `zoom clear` kept the previous zoom's samples

- Command: `profiler-cli zoom push 2.290,3.081 --session browser_fog_rdd.js-1`, `thread samples --include-idle`, then `profiler-cli zoom clear --session browser_fog_rdd.js-1` and `thread samples --include-idle --json` (also without `--json`).
- Expected: the full profile's 138 samples (`thread info` says "138 samples").
- Got: `totalSamples: 13`, the zoomed range's count, under a header saying "View: Full profile". It happened after two separate `zoom clear` calls. It only came right after pushing and popping other ranges (`zoom push 0,6.78` gave 138).
- Workaround: `zoom push <start>,<end>` covering the whole profile instead of `zoom clear`.

## Marker search cannot match the text the list prints for a Runnable's priority

- Command: `profiler-cli thread markers --search "priority: Idle" --list` and `--search "Idle (0)"`.
- Expected: the idle-priority runnables, since the list prints `ChromeUtils::IdleDispatch - priority: Idle (0) task: ...`.
- Got: "No markers match". The printed line is built from fields, and only `priorityName:Idle` matches (found by reading `marker info --json`).
- Question behind it: "which idle-priority tasks ran in this range?" It would help if the list showed the field keys, or if bare search terms also matched the printed description.

## `marker info` does not print the marker index a profiler link uses (review-browser_fog_rdd.js)

- Command: `profiler-cli marker info m-122 --session review-browser_fog_rdd.js-1`, to check that a report's link `marker=37580` points at the marker it quotes.
- Expected: the marker's index in the thread's marker table, next to its handle.
- Got: name, time, fields and description, but no index. Only `--json` has `markerIndex`, so each link check took `--json` piped through a script (10 links in one profile). That is how it turned out the "gate" links point at the `NotifyObservers` inside the gate runnable (index 37580), not at the runnable (37579).
- Question behind it: "which marker does this link's `marker=N` open?" Printing `markerIndex` in the default output, or a `marker info --index N` lookup, would answer it directly.

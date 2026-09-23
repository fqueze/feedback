## Times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --search confirmation-hint,startAutoHideTimer --list --limit 0 --session <s>` and `profiler-cli marker info m-812 m-786 m-815 --session <s>` on a 5m14s profile (KJcaHavLRHWjhpuEI7S0_w).
- Expected: times with millisecond precision (e.g. `t=159.189s`), as shown for markers under one minute (`t=28.997s`).
- Got: `t=2m39s` for every row and in `Time: 2m39s - 2m39s`, so a `popupshowing`, a timer callback and a `popuphiding` 16 ms apart cannot be ordered. The "Captured at: 2m36s" of a marker stack has the same problem.
- Workaround: `marker info ... --json` and reading `start`/`end` in ms.

## Question: which marker index a profiler link's `marker=N` points to (review)

- Command: `profiler-cli marker info m-517 --session <s>` to check that a report's link `marker=311050` is this marker.
- Expected: the marker index shown in the text output, since links carry it.
- Got: the text output has no index; only `marker info m-517 --json` has `markerIndex`. So I scripted one `--json` call per marker to check 20 links.
- Could have shown: `Index: 311050` in `marker info`, or a `marker find <index>` / `--marker-index N` lookup.

## Question: which event started each of these timer callbacks (review)

- Command: `profiler-cli thread markers --search startAutoHideTimer --list --limit 0 --session <s>`, then one `marker info` per row to read `Captured at`.
- Expected: for markers that have a stack, the list to say when the stack was captured, so that timers can be matched to the `popupshown` that armed them.
- Got: only a ✓ for "has stack". I needed 11 `marker info --json` calls to see that all 11 were captured at the same `popupshown`.
- Could have shown: a `captured=<t>` column in `--list` for markers with a stack.

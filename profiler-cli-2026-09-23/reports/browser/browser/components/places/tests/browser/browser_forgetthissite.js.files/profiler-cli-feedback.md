## `thread markers --list` prints minute-scale times rounded to the second

- Command: `profiler-cli thread markers --session <s> --search "places.sqlite,name:TEST-,sqlite_store" --list --limit 0`
- Expected: times precise enough to order markers within the same second, e.g. whether a `Counter::add sqlite_store.query … busy` came before or after a `TEST-UNEXPECTED-FAIL`, or whether a DeferredTask spans them.
- Got: `t=2m19s` / `t=2m20s` for every row after the first minute, so the order inside the second was lost.
- Workaround: `--list --json` and read `start`/`duration` from `flatMarkers`, through a Python one-liner.
- Question the default output could not answer: "in what order did these markers happen, and does interval X cover instant Y?"

## No way to ask when a given stack was sampled

- Command: `profiler-cli thread samples-top-down --session <s> --search "RemoveItem" --include-idle`
- Expected: a way to get the time(s) of the matching samples (e.g. `--list` with timestamps, or first/last sample time per node).
- Got: a call tree only; to learn whether a `nsNavBookmarks::RemoveItem` sample was before or after a failure marker I had to bisect with `zoom push`/`zoom pop` over 5 to 8 windows.
- Question: "at what time was the main thread in this stack?"

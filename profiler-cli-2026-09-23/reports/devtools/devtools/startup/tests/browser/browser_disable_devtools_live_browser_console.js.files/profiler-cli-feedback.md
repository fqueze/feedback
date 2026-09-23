## `load` selects a WebExtensions GeckoMain thread by default on a mochitest profile

- Command: `profiler-cli load <per-test mochitest profile URL> --session <id>`
- Expected: the parent process GeckoMain (t-0) selected, since every Test marker is there.
- Got: `Selected thread: t-14 (GeckoMain, WebExtensions)` (and t-15 on the retry's profile), so the first `thread markers --category Test` would have returned nothing.
- Workaround: `thread select t-0` after every load.

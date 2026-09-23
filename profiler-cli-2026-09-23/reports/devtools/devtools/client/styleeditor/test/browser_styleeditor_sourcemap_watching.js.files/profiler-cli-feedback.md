## `load` selects a non-parent GeckoMain by default

- Command: `profiler-cli load .../profile_browser_styleeditor_sourcemap_watching-2.js.json --session ...-2`, then `thread markers --category Test --search ... --list`
- Expected: the parent process main thread selected (as with the first profile of the same job), since that is where the test log lives.
- Got: `t-18 (GeckoMain, WebExtensions)` selected, and "No markers match the specified filters", which reads like the test left no log.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.

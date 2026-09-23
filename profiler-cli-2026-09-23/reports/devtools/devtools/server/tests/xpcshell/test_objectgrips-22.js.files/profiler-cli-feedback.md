## `zoom push` with a "2m10s,2m14s" range silently zooms to 1 ns

- Command: `PROFILER_CLI_SESSION_OWNER=test_objectgrips-22.js profiler-cli zoom push 2m10s,2m14s --session test_objectgrips-22.js-1`
- Expected: a zoom to 130–134 s, since that is the format `thread markers --list` prints its times in (`t=2m13s`). Failing that, an error.
- Got: `View: ts-1→ts-2 (1.000ns)` and exit 0. The next `thread markers` said "No markers match", which looks like there is nothing there.
- Workaround: `zoom push 130,134`.

## Which tests were running at time t (question the default output could not answer)

- Question: in a resource-usage profile, which `test` interval markers overlap a given instant (the first failed process launch)?
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python filter on start/duration.
- What could show it: a `--overlapping <t>` (or `--at <t>`) filter on `thread markers` for interval markers. I did not check whether `zoom push t-3,t` then `--list` includes markers that start before the zoom range, which might already answer it.

# profiler-cli feedback

## Which link marker index a handle is (review of browser_trigger_listeners.js)

- Question: does handle m-74 correspond to the `marker=1192085` in a report's profiler link?
- Command: `profiler-cli marker info m-74 --session <s>` (and the same for 7 other handles)
- Expected: the marker index shown in the default output, since it is what profiler.firefox.com links use.
- Got: only name, type, time, fields and stack; the index is only in `--json` (`markerIndex`), so checking 12 links took a loop piping `--json` through python.
- Suggestion: print `Index: N` in `marker info`, or a column in `thread markers --list`.

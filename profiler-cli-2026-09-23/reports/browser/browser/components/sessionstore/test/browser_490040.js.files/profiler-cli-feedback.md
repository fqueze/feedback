## Which `marker=N` index does this handle have? (review of browser_490040.js)

- Question: checking a report's link, does handle `m-6326` correspond to the link's `marker=953819`?
- Command: `profiler-cli marker info --session <s> m-6326` prints no index; I had to run
  `profiler-cli marker info --session <s> m-6326 --json | grep -o '"markerIndex": *[0-9]*'`,
  once per handle, since the multi-handle `--json` output is not easily attributed by grep.
- Expected: the default `marker info` output shows `Index: 953819` (the URL's `marker=` value), so
  `marker info m-1..m-9` checks all of a report's links in one call.
- Also useful: `thread markers --marker-index 953819` (or accepting `mi-953819` as a handle) to go
  from a link's `marker=N` straight to the marker.

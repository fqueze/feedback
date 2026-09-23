## Question: which marker does a profiler.firefox.com link's `marker=N` point to? (review-browser_computed_getNodeInfo.js)

- Command: `profiler-cli load '<from-url link with marker=183237&thread=0>' --session X`, then `profiler-cli status`, then `profiler-cli marker info m-11`.
- Expected: `load` (or `status`) to report the marker selected by the URL, e.g. `Selected marker: m-11 (TEST-UNEXPECTED-FAIL, t=9.512s)`, or `marker info` to print the marker's index.
- Got: `load` and `status` show only thread/range. The default `marker info` output has no index; only `--json` has `markerIndex`.
- Workaround: `marker info m-N --json | python3 -c ...` for each candidate marker, to compare `markerIndex` with the link's `marker=`.

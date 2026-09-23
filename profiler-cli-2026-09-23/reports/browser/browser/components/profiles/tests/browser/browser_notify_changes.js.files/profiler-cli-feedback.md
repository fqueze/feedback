## Question: which marker does a link's `marker=N` point at? (review, 2026-09-22)

- Command: `profiler-cli load "<profiler.firefox.com URL ...&marker=549385...>" --session S`, then `profiler-cli marker info m-35 --session S`.
- Expected: `load` to name the handle of the marker selected by the URL's `marker=N`, or `marker info` to print the marker's index, so a link can be checked against the marker a report quotes.
- Got: neither. `marker info` default output has no index; only `--json` has `markerIndex`.
- Workaround: `marker info m-N --json | python3 -c '...print(d["markerIndex"])'` once per link (12 links in this report).

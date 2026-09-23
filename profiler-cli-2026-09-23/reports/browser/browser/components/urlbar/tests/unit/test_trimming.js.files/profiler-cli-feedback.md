
## Question: which profiler.firefox.com `marker=N` index does this handle correspond to? (review-test_trimming.js, 2026-09-22)

- Command: `profiler-cli marker info m-19 m-13 m-110 --session <s>`
- Expected: the marker index used by profiler.firefox.com links (`markerIndex`) in the default output, so a report's `marker=N` link can be checked against the quoted marker.
- Got: default output shows name, type, time, fields, but no marker index; only `--json` has `markerIndex`, and checking several markers meant a python script over the JSON.
- Workaround: `marker info ... --json | python3 -c '...print(m["markerIndex"])...'`.


## Which marker does a link's `marker=N` point at?

- Question: checking a report's link, "is the link's `marker=N` the marker the report quotes?"
- Command: `profiler-cli load '<url with marker=201826>' --session S`, then `profiler-cli marker info m-1030 --session S`
- Expected: `load` to say which marker the URL selects (handle and summary), or `marker info` to print the marker's index.
- Got: neither; `load` prints only the thread and range, and `marker info` has no index. Needed `marker info m-… --json` piped through a Python one-liner to read `markerIndex`, for 12 markers over 2 profiles.
- Suggestion: print `Index: N` in `marker info`, and have `load` report the URL's selected marker as `m-…`.

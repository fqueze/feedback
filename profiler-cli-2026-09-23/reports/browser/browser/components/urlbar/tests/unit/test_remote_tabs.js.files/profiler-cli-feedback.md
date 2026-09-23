## Question: which marker does a link's `marker=N` point to?

- Command: `profiler-cli marker info m-14 --session …` (default output)
- Expected: the marker's table index (`markerIndex`), so a link's `marker=N` can be matched to the quoted marker.
- Got: name, type, time, fields, but no index; it is only in `--json`, so each check needed `--json | python3 -c '…markerIndex…'`.
- What could show it: print `Index: N` in the default `marker info` output, or accept `marker info --index N` to go straight from a link to the marker.

## Question: "which marker does this link's `marker=N` point at?" (review of a report's links)

- Command: `profiler-cli marker info m-33 --json --session … | python3 -c '…print(d["markerIndex"])'`, looped over each handle found with `thread markers --search`.
- Expected: a way to go from a link's `marker=N` to a handle directly (e.g. `marker info --index N`), or `markerIndex` in the default `marker info` / `--list` output.
- Got: the default output has no marker index, so checking 13 links took a search per marker plus a JSON script loop.
- Workaround: search for the quoted marker, then compare `markerIndex` from `--json`.

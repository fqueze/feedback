## `thread samples-top-down` on a zoom with no samples prints an empty tree, silently

- Command: `profiler-cli zoom push m-324 --session S` (a 0.57 ms runnable), then `profiler-cli thread samples-top-down --session S --include-idle`
- Expected: "no samples in this range (sampling interval 10 ms)" or similar.
- Got: the header and "Top-Down Call Tree:" with nothing under it; I first took it for a zoom/session problem and re-ran with `status` to check.
- Workaround: none needed once understood; markers carry the evidence at that scale.

## (review) `marker info` does not show the marker index a profiler link's `marker=N` refers to

- Question: "is m-122 the marker a link's `marker=12425` points to?"
- Command: `profiler-cli marker info m-122 --session S`
- Expected: the marker's index in its thread's marker table (the `marker=` URL value) in the default output.
- Got: name, time, fields; the index only in `--json` (`markerIndex`), so checking 20 links took a `python3` one-liner per marker.
- Workaround: `marker info m-N --json | python3 -c '...print(d["markerIndex"])'`.

# profiler-cli feedback

## Question: which marker does a link's `marker=N` point at? (review-browser_923857_customize_mode_event_wrapping_during_reset.js)

- Command: `profiler-cli load "<profiler.firefox.com URL with marker=N>" --session S`, then `profiler-cli marker info m-X --json --session S | python3 -c '…print(markerIndex)'`, once per link (20 links, 4 profiles).
- Expected: `load` reports the marker that the URL's `marker=N` selects, as a handle. Or `marker info` prints the index in its default output.
- Got: `load` reports the thread and range but not the selected marker. The default output of `marker info` has no index.
- Workaround: `--json` plus a python one-liner per handle.
- What could have shown it: `load` printing `Selected marker: m-… (index N)`, or `marker info` printing `Index: N`.

## (review) Question: "which marker is the `marker=N` of this link?"

- Command: `profiler-cli load "<profiler.firefox.com link with marker=10730>"`, then `thread markers --search ...` / `marker info <m-…> --json` to find markerIndex 10730.
- Expected: the load to select or print the marker the link names, or a `marker info --index 10730`.
- Got: no way to go from a markerIndex to a handle. I had to dump 865 markers of a zoomed window with `--json`, then `marker info` all 865 handles, to learn that 10730 was a `not killing` line and not the ERROR the report quoted.
- Could show: `load` printing "link marker: m-… (<name>, t=…)", or `marker info` accepting `index:N`.

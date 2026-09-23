## review-browser_ext_getViews.js: "does one window's refresh driver tick at every vsync over this range?"

- Command: `profiler-cli thread markers --search 'RefreshDriverTick waiting for paint' --group-by 'name,field:innerWindowID'` (zoomed), then `--list --limit 0 --json` piped to a Python script.
- Expected: the per-group count plus the interval min/avg/max that the ungrouped "Frequency Analysis" section already prints per marker name.
- Got: per-group counts and examples only. The frequency figures are per name across all groups, so 3 stray ticks from other windows skew the average, and a script over `flatMarkers` was the only way to get the largest gap for one innerWindowID.
- Workaround: the script over `--json`.

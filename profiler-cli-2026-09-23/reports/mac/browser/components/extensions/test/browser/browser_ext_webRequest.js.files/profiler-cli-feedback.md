
## review-browser_ext_webRequest.js — longest gaps in one window's periodic markers

- Question: during a range, how regularly does one document's `RefreshDriverTick waiting for paint`
  fire (the largest gap between consecutive markers with that innerWindowID), and where are the
  longest gaps across the whole profile?
- Command: `profiler-cli thread markers --search "name:RefreshDriverTick" --group-by "name,field:innerWindowID"`
  gives the count per window, but not the gaps; the per-name `rateStats.maxGap` in `--json` mixes
  all windows and gives no position. I scripted over `--list --limit 0 --json` (24,804 markers).
- Could show: per group of `--group-by`, max gap and the top-N gaps with their start times.

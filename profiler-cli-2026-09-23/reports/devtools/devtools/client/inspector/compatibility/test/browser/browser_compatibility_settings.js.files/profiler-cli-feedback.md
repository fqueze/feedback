## Question: "when did this repeated marker start firing continuously?"

- Command: `profiler-cli thread markers --session <s> --search "name:RefreshDriverTick waiting for paint" --group-by field:innerWindowID`
- Expected: a way to see a marker's rate over time (per-second histogram, or first/last time per group), to find when a stream of skipped ticks began.
- Got: counts per group and a few example handles; `--list` gives 2,509 rows. Also, `--search "name:X,innerWindowID:2"` is OR, so it cannot narrow to one window's markers.
- Workaround: `--list --limit 0 --json | jq` filtering `data.innerWindowID==2`, bucketing `start` per second. Same for CompositorScreenshot, to find the last composite. A `--group-by time:1s` or first/last-start columns in `--group-by` output would have answered it directly.

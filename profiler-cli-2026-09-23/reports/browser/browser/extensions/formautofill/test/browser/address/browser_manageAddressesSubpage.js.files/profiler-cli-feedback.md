# profiler-cli feedback

## Question: "what was the machine's mean CPU over this test?"

- Command: `profiler-cli zoom push <test marker> --session <ru>` then
  `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0 --json | jq ...`
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean/max).
- Got: the aggregate view only gives counts and durations; per-marker rows need `--json` and jq
  to average 619 markers.
- Could show: `thread markers --search name:X` summarizing numeric payload fields (min/mean/max)
  in the aggregate view.

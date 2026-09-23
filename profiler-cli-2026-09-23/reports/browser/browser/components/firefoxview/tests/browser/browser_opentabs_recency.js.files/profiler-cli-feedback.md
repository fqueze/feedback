## Question: the ms-precise start times of markers in a sub-second range (review of browser_opentabs_recency.js)

- Command: `profiler-cli zoom push 179.15,179.53 --session S` then `profiler-cli thread markers --session S --list --limit 0 --search "TabRecencyChange,activate,..."`
- Expected: start times precise enough to order markers a few ms apart, e.g. `179191.6 ms`, or an offset from the zoom start.
- Got: `t=2m59s` on all 39 markers, so they cannot be ordered or measured against each other.
- Workaround: `--json` and a python script printing `flatMarkers[].start`. With `--list`, the output could scale the time precision to the view range.
